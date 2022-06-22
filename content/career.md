---
title: "Career"
date: 2019-11-20T23:38:52+08:00
comments:       false
showMeta:       false
showActions:    false
---

# Overview and Vision

I am a data engineer.

My vision is to automate everything that can be automated, and automating the automation itself.

What I do on a daily basis:
1. tend data pipelines and make data flows efficiently, reliably for my fellow data analysts and data scientists
2. build software solutions to make data more accessible from Data lake/warehouse
3. help explore latest framework / DE solutions
4. people and team management if needed

You should only notice me when something is broken.


### Focus

Based on my understanding that the current data engineering job scope can be divided into three sub fields:
1. Infrastructure
2. Pipeline (with in-house tooling or dynamically generate pipelines)
3. Data Warehouse + Data modelling (more like Analytical Engineering)

I have various level of experience with all three fields, focusing on different parts at different stages in
my career. Currently focusing mostly on #2. 


# Work Experience

### Senior Data Engineer + Pod Lead - Sept 2021 - Present
 
This requires me to work as an IC role, and gradually move towards a team lead position. I am primarily focusing
on delivering Python-based software solutions that automate the building of pipelines, and maintaining the 
quality of our data lakehouse.

Tech stack used:

Orchestrator: Airflow 1 + Airflow 2 (in transition)
Data Lakehouse: Delta Lake + Hive
Ingestion: in-house
Ingestion Engine: Databricks Spark
Transformation Engine: Databricks Spark
Transformation Framework: dbt
Analytical Engine: Presto / Trino
Streaming: Kafka Connect + Debezium
BI: Looker
Monitoring: Datadog + Pagerduty + Slack

Responsibilities:
- Build Python solutions 
    - to make data analytics self-serviceable
    - to abstract complex spark and delta lake usages for data analysts
    - to make generation of batch/streaming pipelines (semi)-automated via configurations
    - to make pipelines auto recover
- Exploring new data solutions (Looking at Airbyte and DataHub now) 
- participate in cross-team projects if needed
- Drafting and driving projects / epics within Pod, Building + setting success metrics, monitoring of success metrics
- other standard management stuff like my last job
- DOCUMENTATIONS!
 
 
### Data Team Lead - June 2020 - Sept 2021

This role requires me to work as a manager with hands on work to setup the data platform infrastructure of a small 
startup from scratch, plus a distributed web-crawling system.

Tech stack used:

Orchestrator: open sourced version of Apache Airflow 2
Data Warehouse: AWS Redshift
Ingestion: in-house
Job Queues: Apache Kafka
Transformation Framework: dbt
BI: Metabase
Metadata Management: Django Admin Page (I did not follow up this part of the data stack that closely back then)

Most of the stuff are setup in house and fully on AWS ECS Fargate.

Engineering Responsibilities:
- Infrastructure setup
- Build pipelines end-to-end
- Exploring new data solutions (lead to adoption of Airflow 2 and dbt)
- Difficult bugs

Management Responsibilities:
- sprint planning
- solution design
- mentoring juniors
- setup and standardize workflow such as bug reporting
- 1:1 with team members 


### Data Engineer - Aug 2018 to June 2020

This role requires me to work both as a backend developer and a data engineer, 
it also requires me to communicate a lot with different stakeholders in 
different functional teams.

Main responsibilities:

Data Engineering

- Maintain (Python, SQL, AWS ECS) batch/stream data pipelines using Apache Airflow 
- Migrated Data Warehouse from legacy Postgres RDS into AWS Redshift
- Design Data Warehouse (AWS Redshift) architecture, data health check and alerts (Emails and Slack) to speed up data analysis, validation, exploration & hypothesis testing
- Data modelling and design optimised analytical tables to speed up B2B data deliveries and data science iteration
- Enable safe automated data deployment into production environment and validation 

Backend & DevOps

- Handle several (Python Falcon and Django) backend services, including data science projects and internal services, deployed in containers (AWS Fargate)
- Implement a data back office to augment automatically processed data with manually cleaned data
- Setup CI/CD processes
- Build a few internal Python libraries shared across projects

Data Analysis

- Build and maintain monitoring dashboards (Metabase, Mode Analytics) for in-app usage, data quality and service performances
- Investigate data anomalies to help the team theorise  / make decisions
- Support marketing and product team with data insights

Data Science

- Maintain, refactor and debug existing data science projects
- Brainstorm improvements / implement based on user inputs and other data metrics


# Professional Skills

legend (being as accurate as I can):

- `Basic understanding`: Never used in production environment 
    but has a general idea on how a solution should be like, able to build POCs and experiment on it
- `familiar`: has production experience, able to design, implement solutions or workflow end to end
- `proficient`: everything from `familiar` + with confidence that this should be the 
    most optimized solution given all factors considered

### Engineering Skills

#### Programming language

- Python 3 - Proficient
    - familiar with Python coding standard, common practices and design patterns
    - thinks type hinting, naming convention and inline docs are critical
    - familiar with Python web backend frameworks
    (Falcon, Django, FastAPI), familiar with RESTful API design

- SQL - Proficient
    - not much to talk about really

#### Tools

- Spark, Presto, Hive - Proficient in usage, familiar with internals
- Kafka - familiar with usage
- Delta Lake - Proficient
- Proficient in Airflow 1 + 2, including internals
- Data Warehouses (Redshift) - Proficient
- Data Lake (Delta + s3) - Proficient
- Relational DB - Proficient in Postgres, MySQL
- NoSQL - Familiar with Mongo and Dynamo
- Familiar with other databases such as redis, AWS Dynamo etc.

#### Data

- Proficient in Data modeling 
- Proficient in modern Data Warehouse solution design (Inmon approach), data pipeline designs
- Proficient in performing common optimization measures like partitioning, bucketing etc.
- Proficient in design and performing Data Quality Checks

General

- Familiar with modern programming toolkit: Unix terminal operations, version control, Docker, setup CI/CD, documentations
- Familiar in commonly used Amazon Web Services (including those that are typically 
out of DE scope such as Cloudfront or networking configuration), able to independently deploy solutions from start to end


### Analytical Skills

- Able to understand and grasp the business context quickly
- Able to understand data, data flow, transform business requirements to technical requirements
- Able to temporarily cover data analysis requests and simple model building 
- Data visualisation

### Other Skills

- Fluent in mandarin and English
- Strong self-learning capability and a desire for knowledge
- Able to communicate with data analysts, data scientists and understand, accept / pushback / propose alternative solutions for their needs
- Able to communicate with non-data, non-technical team members (marketing, sales, upper management) 


# Education Background

### National University of Singapore 

- Bachelor of Science in Business Analytics
- Science & Tech Scholarship