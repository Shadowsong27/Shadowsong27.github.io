---
title: "DevOps Log 0.1: Setup Cloudwatch on EC2 Instance"
thumbnailImagePosition: "left"
thumbnailImage: https://www.sysonion.de/wp-content/uploads/2018/09/cloudwatch.png
coverImage: https://miro.medium.com/max/800/1*ffbynFsxn43lPdne2kaHMQ.png
metaAlignment: left
coverMeta: out
date: 2019-03-28
categories:
- software-engineering
- dev-ops
tags:
- AWS
- Cloudwatch
- EC2
---

I wrote this initially in our internal documentation website, so this is a very short note on how to setup Cloudwatch on AWS EC2. 
<!--more-->

I have not touched EC2 deployment for a very long time since I switched to Fargate, but I guess it’s good to keep a notebook of what I have tried and experienced.

Note: this was written more than one year ago, some of the things may be updated / outdated.  Reference with care.



1. Download the agent package
```
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
```

2. Install package
```
sudo dpkg -i -E ./amazon-cloudwatch-agent.deb
```

3. Configuration using wizard
```
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

4. Check the EC2 instance is attached with an IAM role that has Cloudwatch rights

5. Install `collectd` if not done so (most likely you are not)
```
sudo apt-get install collectd
```

6. Start the Agent with the latest configuration
```
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s
```

To check the status:
```
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a status
```

Response should be something like 
```
{
  "status": "running",
  "starttime": "2019-03-28T04:36:52+00:00",
  "version": "1.208036.0"
}
```


Note a few things:

1. we enable the usage of `statsd` and `collectd`

2. Port of `statsd` should be set to `8127` instead of the default `8125`, which is commonly used by `datadog`  - modern note: If you do not use Datadog I think it is fine, but conflicting ports could be one of the problem one encounters during setup, it is crucial to check the agent logs

Naming convention I use for log group and log stream:

1. Log group name should be the name of the service / ec2 instance 

2. Log stream name should be the functionality of the instance (or logger if you have multiple loggers configured and running in the same instance)

# Troubleshooting

- If the agent is not running for some reason, first thing check the agent log at:
    ```
    sudo tail /opt/aws/amazon-cloudwatch-agent/logs/amazon-cloudwatch-agent.log
    ```

    Usually it provides you with the necessary error message (This is not included in the AWS guide)

- Potential problems

	1. Port usage: other services might have taken the spot, check carefully for the port usage
	
	2. IAM role is invalid or wrong
	
	3. Have two running awslog agent: this guide accounts for the newer `Cloudwatch` agent, so stop the current `awslogs` service using 
```
sudo service awslogs stop
```