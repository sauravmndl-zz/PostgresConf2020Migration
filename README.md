# Enterprise-grade Automated Migration for PostgreSQL on Cloud

SAP Cloud Platform (SCP) is an open platform-as-a-service (PaaS) which facilitates creating new cloud applications or extending existing applications and run them in a secure cloud environment managed by SAP. The SAP Cloud Platform integrates data and business processes. SCP supports multiple Infrastructure-as-a-Service (IaaS) like AWS, Azure, GCP, Alibaba Cloud and OpenStack.

> One of the core platform backing services provided by SCP is PostgreSQL-as-a-Service. SCP manages 10,000+ PostgreSQL-as-a-Service instances across multiple IaaS. Each PostgreSQL-as-a-Service instance consists of VMs to have Postgres cluster - Postgres-Master, Postgres-Standby. Data is replicated asynchronously from Postgres-Master to Postgres-Standby. [BOSH](https://bosh.io/docs/) is used to automate large-scale Postgres deployments across different IaaS.

## Why migration required?

In cloud it is quite often that applications need to consume same database services from a different environment. In that case migration of database is absolute necessary. Then service providers need to come up with a pragmatic solution, keeping few aspects in mind - atomicity, performance and less manual intervention. Most importantly the solution should be infrastructure agnostic, if the cloud vendor supports multiple IaaS. Imagine how hundreds of databases migrated in parallel?

## Automated migration at SAP cloud platform

PostgreSQL stores data to persistent disk attached to the VM. In the VM apart from postgres process, an agent process is run which is responsible for automated migration of entire data on persistent disk to the target postgres server. The agent process is triggered as per user's request, here onwards entire procedure is automated. Agent performs following steps in migration

1. Attaches an auxiliary disk to the source VM
2. Takes backup of data from persistent disk to auxiliary disk
3. Triggers restore to target postgres server from the backup data
4. Deletes auxiliary disk after successful migration

The migration happens in offline mode, application stops accessing data. It is atomic, failure at any step leads to restart of operation. Agent retries to complete successfully, if not manual intervention is required. For backup and recovery pg_dump and pg_restore used respectively. As dedicated agent runs on each source server, it migrates only individual data, and hence performance is not impacted. This automation process allows to migrate many databases in a single-window, and thus reduces operational effort by significant factor. Here is a flow diagram of entire process presented.

![N|Solid](https://github.com/sauravmndl/PostgresConf2020Migration/blob/master/pgconf_migration.png?raw=true)
