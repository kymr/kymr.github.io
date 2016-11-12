---
layout: post
title: 'Security in Hadoop. (Hadoop Summit 2016 summary)'
author: yongmaroo.kim
date: 2016-11-13 05:30
tags: [hadoop,bigdata]
---
## Overview

- Actually, i attended [hadoop summit 2016 - tokyo](http://hadoopsummit.org/tokyo/) at last month. So i am going to review some of the talks that i heard. Also you can download slides and watch the videos from youtube.
- [slides](http://hadoopsummit.org/tokyo/agenda/)
- [youtube](https://www.youtube.com/channel/UCAPa-K_rhylDZAUHVxqqsRA)
  - Unfortunately, just keynote videos were uploaded now. But i think each session's video will be uploaded soon.
- This article is about Security in Hadoop, especially Apache Ranger & Apache Atlas.
  
### Security in Hadoop

There were two sessions for Security in Hadoop. 
- [Security and Data Governance using Apache Ranger and Apache Atlas](http://www.slideshare.net/HadoopSummit/security-and-data-governance-using-apache-ranger-and-apache-atlas) by Madhan Neethiraj. 
- [Protecting Enterprise Data in Apache Hadoop](http://www.slideshare.net/HadoopSummit/protecting-enterprise-data-in-apache-hadoop-67928870) by Owen O`Malley.

Nowadays, many organizations have its own data platform with Hadoop. Maybe many users use the platform at the same time, and it can cause several issues. Security is one of the most important requirement to maintain the system. There are various requirements in Security.

- Administration
  - Central management, Consistent Security
- Authentication
  - Authenticate users and systems
- Authorization
  - Provision access to data
- Audit
  - Maintain a record of data access
- Data Protection
  - Protect data at rest and in motion

There are several good open-source to handle those things.

- [Apache Ranger](http://ranger.apache.org/)
  - Data security framework for Hadoop.
- [Apache Atlas](http://atlas.incubator.apache.org/)
  - API Gateway for Hadoop.
- [Apache Knox](https://knox.apache.org/)
  - Data governance and Metadata framework for Hadoop.

I am going to introduce Apache Ranger & Apache Atlas in this article.
  
### Apache Ranger

#### Goal

- Centralized security administration to manage all security related tasks in a central UI or using REST APIs.
- Fine grained authorization to do a specific action and/or operation with Hadoop component/tool and managed through a central administration tool.
- Standardize authorization method across all Hadoop components.
- Enhanced support for different authorization methods - Role based access control, attribute based access control etc.
- Centralize auditing of user access and administrative actions (security related) within all the components of Hadoop.

#### Centralized Administration

![Centralized Administration](/files/hadoop-summit/security/centralized_administration.png)

- Apache Ranger support web based UI for centralized administration.

#### Authorization Policies

![Authorization Policies](/files/hadoop-summit/security/authorization_policy.png)

- Support easy and consistent way to handle access control across Hadoop components

#### Row-Filter, Column-masking

- Row-Filter
  - Row-filter filter the accessible data only.
  - Let's suppose your organizations have employees at many countries. If you want to grant some user or user group to access specific rows like country, then you can easily set it with row-filter.
- Column-masking
  - Some specific fields like id, password, personal information are very sensitive.
  - You can hide some characters from those fields with column-masking.
  
#### Tag-based Policies

![Tag-based Policies](/files/hadoop-summit/security/tagbased_policy.png)

- Support setting tag with specific rules.
- Is is very simple to maintain, and reusable.

#### Audit logs

![Access Audio Logs](/files/hadoop-summit/security/audit_log.png)

- Apache Ranger plugin generate detailed audit logs of access to protected resources.
- Log destination is also pluggable.
- Support interactive view in Apache Ranger Console.

#### Architecture

![Apache Ranger Architecture](/files/hadoop-summit/security/ranger_architecture.png)


### Apache Atlas

Atlas is a scalable and extensible set of core foundational governance services – enabling enterprises to effectively and efficiently meet their compliance requirements within Hadoop and allows integration with the whole enterprise data ecosystem.

- Metadata Repository
  - Flexible type system to capture schema/metadata of multiple components
  - Out-of-box models for Hive, HDFS, Storm, Falcon, Sqoop
- Data Lineage/Provenance
  - Captures data lineage across components
- Classification
  - Use tags to classify the data – like PII, PHI, PCI, EXPIRES_ON
  - Support for attributes in tags – like expiry_date
- Search
  - Search using classifications, attributes
  - Advanced search using DSL; convenient full-text search
- Integrations
  - With Apache Hive, Apache Storm, Apache Falcon, Apache Sqoop for metadata and lineage
  - With Apache Ranger for classification based security
- APIs to add support for more components

#### Lineage

![Lineage at Apache Atlas](/files/hadoop-summit/security/lineage_atlas.png)

- Allowing users to drill-down into operational, security, and provenance related infomation

#### Classification

![Classification at Apache Atlas](/files/hadoop-summit/security/classification_atlas.png)

- Import or define taxonomy business-oriented annotations for data.
- Define, annotate, and automate capture of relationships between data sets and underlying elements including source, target, and derivation processes.

#### Architecture

![Apache Atlas Architecture](/files/hadoop-summit/security/atlas_architecture.png)