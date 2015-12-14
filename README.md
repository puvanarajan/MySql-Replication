# MySqlReplication

    This is small tutorial for MySql replication. Here I am using three servers.
        Master Server A : **192.168.1.10**
        Slave server B  : **192.168.1.11**
        Slave server C  : **192.168.1.12**

**IF MYSQL SERVER ALREADY INSTALLED PLEASE AVOID STEP 1 TO 4**
**IF MYSQL SERVER DON'T HAVE ANY EXTERNAL ACCESSING YOU HAVE TO DO THE STEP 4**

##### Master server configuration #####
Step 1: Install the mysql server

    #yum install mysql-server 
    #chkconfig mysqld on

Step 2: We need to restart the MySql server

    #service mysqld start
    
Step 3: After the mysqld service has been started, set your mysql server root password by using following commands

    #sudo /usr/bin/mysql_secure_installation

Step 4: Server we need give the port accessing permission. By default MySql server is using port 3306. So we need to add this port iptable.<br> 
**NOTE: If you change the default port, then you have to add the default port to iptables.**

    #iptables -I RH-Firewall-1-INPUT 10 -p tcp --dport 3306 -j ACCEPT
    #service iptables save 

Step 5: Edit my.cnf file and add the following entries. Here you can use your favourite editor. I am using VI editor

    #vi /etc/my.cnf
  and check following lines

    [mysqld]
    bind-address=192.168.1.10 #MASTER SERVER ADDRESS
    server-id = 1 #UNIQUE ID
    binlog-do-db=mydb #DATABASE NAME
    relay-log = /var/lib/mysql/mysql-relay-bin
    relay-log-index = /var/lib/mysql/mysql-relay-bin.index
    log-error = /var/lib/mysql/mysql.err
    master-info-file = /var/lib/mysql/mysql-master.info
    relay-log-info-file = /var/lib/mysql/mysql-relay-log.info
    log-bin = /var/lib/mysql/mysql-bin
  
Step 6: Restart MySql server

    #service mysqld restart
    
Step 7: Open MySQL shell and login

    #mysql -u root -p <br>

   **NOTE : If your are using plesk, you can use this command**

    #mysql -uadmin -p`cat /etc/psa/.psa.shadow`
  
Step 8: We need to grant privileges to the slave. You can use this line to name your slave and set up their password. The command should be in this format

    #GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'slave_user_password';
    #FLUSH PRIVILEGES;
    #USE mydb;
<br>Following that, lock the database to prevent any new changes:

    #FLUSH TABLES WITH READ LOCK;
    #SHOW MASTER STATUS;

 Output like this
 
    +------------------+----------+--------------+------------------+
    | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
    +------------------+----------+--------------+------------------+
    | mysql-bin.000123 |      111 | mydb         |                  |
    +------------------+----------+--------------+------------------+
    1 row in set (0.00 sec)

<br>This is the position from which the slave database will start replicating. Record these numbers, they will come in useful later.

Step 9: Open new tap (Please keep the MySQL shell)

    #mysqldump -u root -p --opt mydb > mydb.sql
    
If you are using plesk

    #mysqldump -uadmin -p`cat /etc/psa/.psa.shadow` --option mydb > mydb.sql

Step 10: After step 9 process completed go to the previous window (MySQL shell window)

    #UNLOCK TABLES;
    #quit;

Step 11: Copy the file to slave server

    scp /path/for/mydb.sql user@192.168.1.11:/root/
    Here 192.168.1.11 is slave server ip and 'user' is slave server user. 
    
##### Slave server configuration #####
Step 1: Open the MySQL shell

    #mysql -u root -p <br>

   **NOTE : If your are using plesk, you can use this command**

    #mysql -uadmin -p`cat /etc/psa/.psa.shadow`

Step 2: Create the database

    #CREATE DATABASE mydb;
    #EXIT;
    
Step 3: Import the database that you previously exported from the master database.

    #mysql -u root -p mydb < /path/to/mydb.sql
    
     **NOTE : If your are using plesk, you can use this command**

    #mysql -uadmin -p`cat /etc/psa/.psa.shadow` mydb < /path/to/mydb.sql
    
Step 4: Now we need to configure the slave configuration in the same way as we did the master

    #vi /etc/my.cnf
  and check follwing lines

    server-id = 2
    master-host=192.168.1.10
    master-connect-retry=60
    master-user=slave_user
    master-password=slave_user_password
    replicate-do-db=mydb
    relay-log = /var/lib/mysql/mysql-relay-bin
    relay-log-index = /var/lib/mysql/mysql-relay-bin.index
    log-error = /var/lib/mysql/mysql.err
    master-info-file = /var/lib/mysql/mysql-master.info
    relay-log-info-file = /var/lib/mysql/mysql-relay-log.info
    log-bin = /var/lib/mysql/mysql-bin
    
Step 5: Restart MySql server

    #service mysqld restart
    
Step 6: Open MySQL shell again

    #CHANGE MASTER TO MASTER_HOST='192.168.1.10',MASTER_USER='slave_user', MASTER_PASSWORD='slave_user_password',        
    MASTER_LOG_FILE='mysql-bin.000123', MASTER_LOG_POS=  111;
    # START SLAVE;
    # SHOW SLAVE STATUS\G
    OUTPUT LIKE THIS
    mysql> SHOW SLAVE STATUS\G
    *************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 88.208.192.220
                  Master_User: badb2015
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000019
          Read_Master_Log_Pos: 38646183
               Relay_Log_File: mysql-relay-bin.000005
                Relay_Log_Pos: 13582866
        Relay_Master_Log_File: mysql-bin.000019
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB: dnjaffnabat
          Replicate_Ignore_DB: 
           Replicate_Do_Table: 
       Replicate_Ignore_Table: 
      Replicate_Wild_Do_Table: 
      Replicate_Wild_Ignore_Table: 
                   Last_Errno: 0
                   Last_Error: 
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 38646183
              Relay_Log_Space: 15535821
              Until_Condition: None
               Until_Log_File: 
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File: 
           Master_SSL_CA_Path: 
              Master_SSL_Cert: 
            Master_SSL_Cipher: 
               Master_SSL_Key: 
        Seconds_Behind_Master: 0
        Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error: 
               Last_SQL_Errno: 0
               Last_SQL_Error: 
               1 row in set (0.00 sec)

 **NOTE : If your are using plesk, you can use this command**
 If slave already running it is giving the error. So if their any error use STOP SLAVE and try Step 6 again
 
 **DO SAME AS SERVER C and etc...**
