   # RDS with Wordpresss App
   
   **Relational Database Service (RDS)** is one of types of databases in today's IT world. RDS service is among popular service of AWS which is **managed** by AWS
   itself.
      
   As we create RDS service on AWS Console, we will see that we need to choose our engine to work with. This could be:
   
   -  **Amazon Aurora, MySQL, MariaDB, PostGresSQL, Oracle, or Microsoft SQL Server**

   That means we do not need to configure anything related to DB because everything is **managed by AWS.**
   
   Usually when we work with DBs, we have **MASTER** and **STANDBY** (Agent) **DBs.** 
   
   In order to achieve High Availability (HA) we rather to have **one master** and **multiple standby DBs**. In case DB goes down, process should not be 
   interrupted. After RDS realize that loss, one of Standby DBs will become master DB and RDS will spin up one more Standby (read only) DB for the cluster.
   
   DBs are one of the most expensive AWS services.
   
   -  PRODUCTION -------> We tend to use MEMORY OPTIMIZED CLASSES (r classes)
   -  LOWER ENV. -------> We tend to use BURSTABLE CLASSES (t classes)
   
   **MEMORY OPTIMIZED CLASSES:** Accelerates performance for workloads that process large data sets in memory.
   
   **BURSTABLE CLASSES:** Whereas burstable classes provides baseline level of CPU performance with ability to burst above baseline.
   
   While you are creating DB, make sure you choose replica of your DB in differentt AZ for **Multi-AZ Deployment** to achieve **HA.**
   Another must have feature when RDS being created is that **Enabling Encryption.** This feature will allow us to secure our data **in Rest.** 
   Lastly, it is strongly recommended to have **Deletion Protection** as well. Then we will prevent our DB from accidental deletion. 
   
   After creating our DB, we will see two DBs under Databases dashboard. By default, two DBs are created because we chose multiple AZs. One is master (writer instance) other one is standby (reader instance). 
   
   Then go to Key Management Service **(KMS)** and get KMS key. 
   
   Next, we need to connect to DB, so we can use **MySQL Client.** 
   
   Now, lets spin up one more instance for MySQL Client in same region because KMS as well as PEM files are regional products we can leverage.
   
   After spinning up your instance, download MySQL client for EC2. 
   
   Then if we have existing Security Group (SG) add one more inbound rule to your SG. Otherwise, create one and add one more port no to your inbound rule.
   For MySQL DB default port no is **3306.**
   
   After installing MySQL successfully, we can look at databases existing or we can create one. 
   
   -  show databases
   -  use sharks
   -  CREATE DATABASE myDB
   -  insert into myDB

   **_Important Note:_** Whenever we create a DB, we have to put it in **Private Subnet** because DBs are always must be private.
   
   In order to put our DB in Private Subnet, we need to create **Subnet Group** from Private Subnets only. Whenever we spin up our DB, it picks up **Private Subnet by default.**



  
   
   
   
   
   
   
   
   
