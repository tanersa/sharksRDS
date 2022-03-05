   # RDS with Wordpress App
   
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
   
   In order to put our DB in Private Subnet, we need to create **Subnet Group** from Private Subnets only. Whenever we spin up our DB, it picks up **Private Subnet    by default.**

   Overall, when we have managed DB, we just choose our **instance type**, our **subnet group** and **KMS key** then rest is taken care by AWS.
   
   No matter what, we need to back up our DB, so we can do that by having **Snapshot**. 
   
   -  To create a snapshot, just go to your DB you created on AWS Console then choose your DB instance (writer or reader) and click on Actions then choose **"take        snapshot"** option and do the steps accordingly. You can even restore your snapshots if you go back to snapshots and choose **"existing snapshot"** then click    on Actions.

   Let's create **Event Subscription**, so we get notification if something happens in RDS. **Event Subscription** is basically Simple Notification Service (SNS) 
   on AWS Dashboard. You can integrate SNS with different AWS Services:
   
   -  **CloudWatch**
   -  **S3**
   -  **RDS**
   -  **and more**

   In order to create Event Subscription, you need to have two things:
   
   -  **SNS Topic**
   -  **SNS Subscription**

   Then, you'll get confirmation email that proves you're the legitimate person. 
   
   If you would like to verify your SNS, you can update anything on RDS such as "Deletion Protection" or "Encryption". Then check your email if you really get          notification regarding that update.
   
   **Let's build a solution for RDS:**
   
   -  Create your VPC
   -  Create 6 subnets ( 2 Public, 2 Private for AppLayer, and 2 Private for DBLayer)
   -  Create your Internet Gateway (**IGW**)   
   -  Create your public and private Route Tables (**RTs**)
   -  Configure your public and private RTs (Add necessary routes and do subnet association)
   -  Create your **NAT Gateway**
   -  Create one more RT for RDS DB Instance (Add necessary routes and do subnet association)
      
      Till now, we build our minimum VPC arhitecture.
      
   -  First, we need to create our Subnet Group because in order to create our RDS DB, we will need to choose our subnet group. 
   -  Choose 2 Private DBlayer Subnets for this Subnet Group and create your Subnet Group.
   -  Let's create our RDS DB cluster now but this time we will use **Public Snapshots** to create our RDS DB, not create RDS from scratch.   
   -  Search for **"wordpress-database"** in filter.
   -  Find "wordpress-database" snapshot related to wordpress app.
   -  Check the checkbox for it and click on Actions drop-down then choose **Restore Snapshot**.
   -  This time we don't create a cluster, we create DB instance. 
   -  Choose Multi-AZ Deployment
   -  Choose your subnet group you just created.
   -  DB instance class: Burstable Class
   -  Log Events: Audit Logs, General Logs, Error Logs, and Slowquery Logs.
   -  Hit **"Restore DB instance"**
   
      Now, we succesfully created our RDS DB instance.
   
      If you would like to verify, go to your security groups and check if its created. It should give us Private IP since its private. 
   
   -  Create security group for your **Bastion Host**  instance.  
   -  Create security group for your Application Load Balancer (**ALB**).  
   -  Create WebServer Security Group
   -  Create DB instance Security Group
   -  If you did not choose **DB instance Security Group** while creating your RDS then modify your security group.

      Continue and Apply Immediately.
      
      Verify your DBs are up and running. It would take some time for that.
      
   -  Configure Auto Scaling Group **(ASG)** and Launch Configuration **(LC)** for **Private Subnets**.  

      While configuring your **Launch Configuration**, we add our **User Data** which is **BootStrap Script**:
      
      
            wordpress-userdata.sh
            
            #!/bin/bash -xe

            yum update -y 

            # Check Amazon Linux 1 or 2 
            isl2=$(uname -a | grep amzn2)

            if [ "$isl2" != "" ] ; then 
                #Amazon Linux 2
                amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
                yum install -y httpd mariadb-server 
            else 
                #Amazon Linux 1
                yum install -y httpd24 php70 mysql56-server php70-mysqlnd
            fi 

            groupadd www
            usermod -a -G www ec2-user 

            #Download wordpress site & move to /var/www/html
            cd /var/www 
            curl -O https://wordpress.org/latest.tar.gz && tar -zxf latest.tar.gz 
            rm -rf /var/www/html 
            mv wordpress /var/www/html 

            #Set the persmissions
            chown -R root:apache /var/www 
            chmod 2775 /var/www 
            find /var/www -type d -exec chmod 2775 {} +
            find /var/www -type d -exec chmod 0664 {} +

            echo '<?php phpinfo(); ?>' > /var/www/html/phpinfo.php 
            service httpd start 
            chkconfig httpd on 

            if [ "$isl2" != "" ] ; then 
                #Amazon Linux 2
                service mariadb start 
                chkconfig mariadb on 
            else 
                #Amazon Linux 1
                service mysqld start 
                chkconfig mysqld on
            fi 

      
      **Explanation of BootStrap Script:**
      
      We start our user data with **Bash Scripting.**
      
            #!/bin/bash -xe
      
      Then we update our packages for linux using yum module.
      
            yum update -y 
      
      Next, we create a variable using shell scripting for Linux machine.
      
            isl2=$(uname -a | grep amzn2)

      Then, place a condition where our script will pick up the correct install packages. If its Amazon Linux install mariadb server. Otherwise, install mysql 
      install packages.

            if [ "$isl2" != "" ] ; then 
                #Amazon Linux 2
                amazon-linux-extras install -y lamp-mariadb10.2-php7.2 php7.2
                yum install -y httpd mariadb-server 
            else 
                #Amazon Linux 1
                yum install -y httpd24 php70 mysql56-server php70-mysqlnd
            fi 
      
      **_Note:_** Good thing about shell scripting is that you can integrate with **Python** and all **other programming languages**. Therefore, integration is 
      so great!
      
      We create a new user group called **"www"** and we add **ec2-user** to **"www"** group.
            
            groupadd www
            usermod -a -G www ec2-user 
          
          
      Download wordpress site & move to /var/www/html
      
            cd /var/www 
            curl -O https://wordpress.org/latest.tar.gz && tar -zxf latest.tar.gz 
            rm -rf /var/www/html 
            mv wordpress /var/www/html      
      
      
      Set the persmissions for read, write, execute (2775)
      
            chown -R root:apache /var/www 
            chmod 2775 /var/www 
            find /var/www -type d -exec chmod 2775 {} +
            find /var/www -type d -exec chmod 0664 {} +
            
      Print info using phpinfo, then start apache service and run service even during boot time.
      
            echo '<?php phpinfo(); ?>' > /var/www/html/phpinfo.php 
            service httpd start 
            chkconfig httpd on 
            
      We are using our end to end solution.
      
      Now, we  start mariadb or mysql server based on Amazon Linux 1 or Amazon Linux 2 and do it even during boot time.
      Lastly, close the shell script with keyword **"fi"**
      
             if [ "$isl2" != "" ] ; then 
                #Amazon Linux 2
                service mariadb start 
                chkconfig mariadb on 
             else 
                #Amazon Linux 1
                service mysqld start 
                chkconfig mysqld on
             fi 
      
   -  Choose existing security group for your LC.
   -  Choose your key pair for the same region.
   -  Do not assign any public ip adress.
   -  Hit "Create Launch Configuration" button. 
   -  Now, we need to create Auto Scaling Group **(ASG)**. 
   -  We need 2 ASG. One is for WebServer instance and other one is for Bastion Host instance.
   -  We also create **Target Group**.
   -  Then create Application Load Balancer **(ALB)**.
   -  Add your **ALB** to your **ASG**.
   
   

      Verify if your **Application Load Balancer** is running. 
      Check your **Target Group** if your targets are healthy.
      
      Our solution worked !
      
      
      
      
      
      
      
      
      
      
      
      
      

   
      


   
  
   
   
   
   
   
   
   
   
