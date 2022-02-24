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
   
   
   
   
   
   
