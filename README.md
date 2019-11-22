# Enterprise-grade Automated Migration for PostgreSQL on Cloud

SAP Cloud Platform (SCP) is an open platform-as-a-service (PaaS) which facilitates creating new cloud applications or extending existing applications and run them in a secure cloud environment managed by SAP. The SAP Cloud Platform integrates data and business processes. SCP supports multiple Infrastructure-as-a-Service (IaaS) like AWS, Azure, GCP, Alibaba Cloud and OpenStack.

> One of the core platform backing services provided by SCP is PostgreSQL-as-a-Service. SCP manages more than 10000 PostgreSQL-as-a-Service instances across multiple IaaSs. Each PostgreSQL-as-a-Service instance consists of VMs to have Postgres cluster - Postgres-Master, Postgres-Standby. Data is replicated asynchronously from Postgres-Master to Postgres-Standby. [BOSH](https://bosh.io/docs/) is used to automate large-scale Postgres deployments across different IaaSs.

## Why migration required?

In cloud it is quite offen that applications need to consume databse services from a different environment. In that case migration of database is absolute necessary. Then service providers need to come up with a pragmatic solution keeping few aspects in mind - atomicity, performance and less manual intervention. Most importantly the solution should be infrastructre agnostic, if the cloud vendor supports multiple IaaS.

## Automated migration at SAP cloud platform

PostgreSQL stores data to persistent disk attched to the VM. In the VM aparat from postgres process, an agent process is run which is responsible for automated migration of entired data on persistent disk to the target postgres server. The agent process is triggered as per user's request, here onwards entire procedure is automated. Agent performs following steps in migration

1. Attaches an auxiliary disk to the source VM
2. Takes backup of data from persistent disk to auxiliary disk
3. Triggers restore to target postgres server from the backup data
4. Deleted auxiliary disk after sucessful migration

The migration happens in offline mode, application stops accessing data. It is atomic, failure at any step leads to restart of operation. For backup and recovery pg_dump and pg_restore used respectively. Here is a flow diagram of entire process presented.

<img src="https://github.com/akashkumar58/pgconf/blob/master/backup-status.png" width="420" align="left"> <img src="https://github.com/akashkumar58/pgconf/blob/master/backupStatus.png" width="420" float="right">

