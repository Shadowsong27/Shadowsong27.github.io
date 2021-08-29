---
title: "DE Log 7: Migrating from Airflow 1 to 2"
thumbnailImagePosition: "left"
metaAlignment: center
thumbnailImage: https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQfrNga0t02Y9d4NitnHpGSEmOWiXKE8v9lxVY1m5iejCrXP9LmlImHza0vkuy46iIOUaQ&usqp=CAU
coverImage: https://airflow.apache.org/images/feature-image.png
coverMeta: out
date: 2021-04-29
categories:
- data
- data-engineering
tags:
- airflow
---
Today I have successfully migrated my current Airflow setup from v1.10.14 to v2.0.2. This article will not be a very detailed step by step guide for upgrading, instead I will introduce the general migration step worked very specifically for my setup, and share some of the problems I encountered during the process, and finally some general feelings with Airflow 2.
<!--more-->

# Deployment Setup

My current setup in production is a mini Airflow cluster hosted on AWS, one master node that contains the `webserver`, `scheduler`, `flower`, and an autoscaling worker cluster, deployed on AWS Fargate. Redis and Postgres Metadata database are deployed separately into existing resources.

Locally, it is very similar except every resource is grouped in a single docker-compose file, and the decoupled DAGs folder is directly mounted into those containers. Thus I can have an update of the pipelines without restarting the docker-compose.

Deployment of DAGs is also slightly adjusted from the common standalone setup. Instead of deploying the scheduler, plugins and DAGs in the same repo, which would inevitably slow down the iteration of common pipeline DAGs, I decoupled the DAGs out into a separate repo, synced inside the master and worker node containers using a simple bash script. I kept the `plugins` inside the repo because you know, they are not going to reload anyway, and they change so infrequently.

My goal here is to first migrate this setup completely into Airflow 2, without changing much with the setup. Then I want to implement the HA and smart sensor setup, highlighted by the documentations as the star improvements in Airflow 2. Along the way I might start to move out of Fargate as I start to feel the limitation of Fargate, but definitely not ECS with EC2 as it is quite bulky too. So the only sensible choice in the end is k8s (sounds exciting).

I created a new repo for hosting the scheduler level code such as Dockerfile, deployment script etc.

# Steps

### Extend the original Airflow docker image and start a local Airflow instance

Originally I build my own version of the image based on puckel's, thus with the new official I image, I need to dive into the Dockerfile and extend it, most importantly it's the sync script I mentioned previously.


#### trouble migrating plugins

In Airflow 1 plugins are imported at run time (if my understanding is of the architecture is sound), and it is imported from Airflow's own module `operators` `hooks` etc. In Airflow 2 they changed it just as if it's a normal python module. However, I have some problems because my DAGs are separated from my plugins, tried to bake them into the same container even logically they are coming from different repo. It did not work for me, so I say ok let's go to the other approach: installing those plugins as python package into the scheduler environment which works pretty well until ...

#### trouble installing additional python packages into Airflow Environment

This took me a while because during my last extension of puckel's image, I switched the user from `airflow` to `root` when building the environment inside the container - I know I know it's not the safest but still I did it and then I forget all about this until the webserver told me I have missing dependencies. Then naturally I bashed into the container and pip list grep the whole thing. The package was there so I was pretty confused, maybe after 10mins or so my mind started to think along the line of the existence of multiple python environments, hence multiple python binaries. I went to check the Dockerfile - nah nothing related to virtual environment which I kinda expected. Then it clicked, it's the system user, and thus it's using different python binary. by using `root` to build system level dependencies and switched it back to `airflow` when building python packages, it worked pretty well

#### trouble deploying using git

This is a bit embarrassing but because the last time I did Airflow setup it was one year ago,  and it costs me 10 mins to realise I forgot to enable the deployment key in Gitlab

#### trouble with SQLAlchemy

SQLA upgraded recently to 1.4, I did not follow it since I don't really have the time to upgrade my current projects. So I sticked to the 1.3.0 version, until I have this bug.

```
Something bad has happened.
Please consider letting us know by creating a bug report using GitHub.

Python version: 3.6.13
Airflow version: 2.0.2
Node: 057953b46ef2
-------------------------------------------------------------------------------
Traceback (most recent call last):
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/app.py", line 2447, in wsgi_app
    response = self.full_dispatch_request()
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/app.py", line 1952, in full_dispatch_request
    rv = self.handle_user_exception(e)
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/app.py", line 1821, in handle_user_exception
    reraise(exc_type, exc_value, tb)
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/_compat.py", line 39, in reraise
    raise value
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/app.py", line 1950, in full_dispatch_request
    rv = self.dispatch_request()
  File "/home/airflow/.local/lib/python3.6/site-packages/flask/app.py", line 1936, in dispatch_request
    return self.view_functions[rule.endpoint](**req.view_args)
  File "/home/airflow/.local/lib/python3.6/site-packages/airflow/www/auth.py", line 34, in decorated
    return func(*args, **kwargs)
  File "/home/airflow/.local/lib/python3.6/site-packages/airflow/www/views.py", line 497, in index
    filter_dag_ids = current_app.appbuilder.sm.get_accessible_dag_ids(g.user)
  File "/home/airflow/.local/lib/python3.6/site-packages/airflow/www/security.py", line 271, in get_accessible_dag_ids
    [permissions.ACTION_CAN_EDIT, permissions.ACTION_CAN_READ], user
  File "/home/airflow/.local/lib/python3.6/site-packages/airflow/utils/session.py", line 70, in wrapper
    return func(*args, session=session, **kwargs)
  File "/home/airflow/.local/lib/python3.6/site-packages/airflow/www/security.py", line 285, in get_accessible_dags
    .subqueryload(Role.permissions)
AttributeError: '_UnboundLoad' object has no attribute 'options'
```

In one of my customized Python dependencies I used v1.3.0 but Airflow 2.0.2 uses v1.3.14, this caused the bug.

### Preparing New Metadata database

Once the DAGs are all reading fine in the local environment, it's time to deploy. However, we have running DAGs throughout the day and to minimize the downtime, I decided to do a mirror deployment with every resource created separately for Airflow 2, new metadata database (not new RDS instance, just a separate DB, same applies for redis).

I also want to keep the logs because you know how things gets out of control if you have a old start date and there are no execution records - DAG run records. So I need to replicate the metadata database.

I created a new branch in my DAG repo, so the actual `production` branch which still used the Airflow 1 import syntax will not be affected by the deployment.

And finally once the DAG is running on the new Fargate service with the new branch, I can turn the Airflow 1 setup off.

Sounds like a good plan and here we go.

1. create TWO replica of existing metadata database in your production database, grant the necessary db access rights, connect to the new prod meta db LOCALLY. The 2nd replica is a backup, so that you won't need to cut off every connection in the current production in order to re-do the replication

    PAUSE ALL DAGS! OR the active state will be replicated into your new replica, which will inevitable auto start some of the DAGs in your local Airflow instance - we don't want that because here we are just testing the database setup, not the DAGs.

    ```sql
    -- revoke access so we can successfully terminate all connections in the prod
    REVOKE CONNECT ON DATABASE airflow FROM airflow;
    REVOKE CONNECT ON DATABASE airflow FROM public;
    
    SELECT
        pg_terminate_backend(pid)
    FROM
        pg_stat_activity
    WHERE
        -- don't kill my own connection!
        pid <> pg_backend_pid()
        -- don't kill the connections to other databases
        AND datname = 'airflow'
        ;
    
    CREATE DATABASE airflow_db_v2 TEMPLATE airflow;
    CREATE DATABASE airflow_db_v3 TEMPLATE airflow;
    
    -- grant it back because you want your current prod Airflow to continue
    -- to work, you might already received some alerts from the current
    -- Airflow, let it auto-heal / redeploy if needed
    
    GRANT CONNECT ON DATABASE airflow TO airflow;
    GRANT CONNECT ON DATABASE airflow TO public;
    
    GRANT ALL ON DATABASE airflow_db_v2 TO airflow;
    GRANT ALL ON DATABASE airflow_db_v3 TO airflow;
    ```

2. Modify in the new repo airflow.cfg to use the correct celery_result_backend and also the sql_alchemy_conn to the newly created DB

3. temporarily export AIRFLOW_HOME to the new scheduler repo - this is small but important

4. run in the new local airflow virtual env, or start a new container to execute the upgrade DB command  `airflow db upgrade` . I did run into another problem while upgrading:

    ```
    sqlalchemy.exc.IntegrityError: (psycopg2.errors.NotNullViolation) column "conn_type" contains null values
    ```

    Well it's not an alien error message, so you simply just understand and navigate in the Metadata database and realise ok one of my existing Airflow Connections have a NULL conn_type, thus during alembic migration it failed and throws this. This is probably due to some legacy issue since I believe I started my cluster from v1.10.10?
    
    At this point, we have tested that the new pipelines more or less works with the new scheduler and the new database, we can then actually deploy it.

### Mirror Deployment

1. Updated my deployment script, in this case my Fargate Task Definitions. Switched the credentials etc so the new deployment is pointing to the new DB. Note here that the executing CMD has changed, `worker` → `celery worker` and `flower` → `celery flower`
2. In local Airflow, switch to use an actual git synced non-production branch you created earlier, instead of using the mounted DAGs
3. Deploy the image and create necessary AWS resources e.g. TD, aws log group and stream - if you have existing cloudformation script / terraform solution, that's great, mine is semi automated so i still need to create something, which is error-prone
4. Once deployment is successful, you can consider start the DAGs all together if there are not much discrepancies between your current state of logs and the current Airflow 1 instance
5. I did another full replication - wait until all pipelines are done - pause everything - switch connection locally and apply replication and then alembic update

Another interesting issue I wanted to mention here before go to bed (this is getting really long ... )

While I was trying help my colleagues set the environment up, one of them cannot load the DAGs despite the DAGs are present in the image, mounted successfully. The only error log is something like `DagFileProcessing terminated after 53.8s` . I did not realise what's this is suggesting initially so I tried every, built from a clean start etc. Until I stopped and start to think

1. not reading DAGs correctly, so the DAG Bag is not getting scanned successfully
2. I remember there is a config controlling the scanning interval, maybe that would help
3. While looking through that particular config, I noticed another config which is the DAG loading timeout , default to be 50s
4. HMMMMMMMM OK this is very similar to the error log, and the computer is pretty laggy, maybe it's the timeout?
5. After increase that config to 200 it starts to load the DAG successfully

# General Feelings

1. Docs are much much much much better. They become readable and reproducible. Some of the earlier gotchas are now documented too. e.g. the execution date, start date and time interval issue.

2. The `quick start` aspect has been improved a lot. Generally it took me a while to fully understand how Airflow 1 works both in Python virtual environment and in containers, with quite some work devoted into maintaining the three different main processes on a EC2 machine. In fact thanks to those experience now I can fairly easy to identify the source of the bugs - whether it's the problem of scheduler or worker, or something related to network.

    Back to the topic, it is now much easier for people to start using Airflow, not just the documentation, they also provide an official container that works out of the box.
    
    Remember the good old `puckel` image in Airflow 1? and the image is very well built too. They have also considered those annoying details such as wait_script etc., which would bothers me quite a lot during the early times too.
    
    They even have purposely shorten the docker-compose command, though I did not use them, I am still using make script, start my local containerized environment with just `make up`.

3. I would say the UI is a big change, user authentication too. Logs now can be opened in a new tab, experimental APIs are finally not experimental - I have not tried this but previously I have quite a few automation thoughts I wanted to do, it is much easier to do with an API exposed.

    I would not really say the UI style has become better due to my personal taste, maybe its a habit problem. There are some rough edges in the `tree view` where the solid line around those small squares looked blurred

4. DAG file are almost fully compatible except the plugins and operator imports. They are pretty easy to change too for me since it's just global bulk replace of import statements.

# Conclusion

I did not spend too much time this due to my decent understand in the deployment of Airflow. Overall it's a pretty rewarding experience, and hopefully I can write another post regarding it's usage, and HA setup.