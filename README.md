# MySqlReplication

###### This is small tutorial for MySql replication

>Here I am using three servers.

Master Server A : **192.168.1.10** <br>
Slave server B  : **192.168.1.11** <br>
Slave server C  : **192.168.1.12** <br>

**IF MYSQL SERVER ALREADY INSTALLED PLEASE AVOID STEP 1 TO 4**
**IF MYSQL SERVER DON'T HAVE ANY EXTERNAL ACCESSING YOU HAVE TO DO THE STEP 4**<br><br>
Step 1: Install the mysql server<br>
  >yum install mysql-server <br>
  >chkconfig mysqld on
<br>

Step 2: We need to restart the MySql server <br>
  >service mysqld start
<br>

Step 3: After the mysqld service has been started, set your mysql server root password by using following commands <br>
  >sudo /usr/bin/mysql_secure_installation

Step 4: Server we need give the port accessing permission. By default MySql server is using port 3306. So we need to add this port iptable.<br> 
**NOTE: If you change the default port, then you have to add the default port to iptables.**<br>
  >iptables -I RH-Firewall-1-INPUT 10 -p tcp --dport 3306 -j ACCEPT <br>
  >service iptables save 
  <br>

Step 5: Edit my.cnf file and add the following entries. Here you can use your favourite editor. I am using VI editor<br>
  >vi /etc/my.cnf <br>
  and check following lines
  <br>
  >[mysqld]
  >bind-address=192.168.1.10 #MASTER SERVER ADDRESS<br>
  >server-id = 1 #UNIQUE ID<br>
  >binlog-do-db=mydb #DATABASE NAME<br>
  >relay-log = /var/lib/mysql/mysql-relay-bin<br>
  >relay-log-index = /var/lib/mysql/mysql-relay-bin.index<br>
  >log-error = /var/lib/mysql/mysql.err<br>
  >master-info-file = /var/lib/mysql/mysql-master.info<br>
  >relay-log-info-file = /var/lib/mysql/mysql-relay-log.info<br>
  >log-bin = /var/lib/mysql/mysql-bin<br>
  
Step 6: Restart MySql server <br>
  >service mysqld restart
  <br>
  
Step 7: Open MySQL shell and login <br>
  >mysql -u root -p <br>

   **NOTE : If your are using plesk, you can use this command**<br>
   >mysql -uadmin -p`cat /etc/psa/.psa.shadow`
  
<br>

Step 8: We need to grant privileges to the slave. You can use this line to name your slave and set up their password. The command should be in this format <br>
>GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'password'; <br>
>FLUSH PRIVILEGES;
<br>
>USE mydb;
<br>Following that, lock the database to prevent any new changes:<br>
>FLUSH TABLES WITH READ LOCK;
<br>
>SHOW MASTER STATUS;
<br> Output like this 
<br>
+------------------+----------+--------------+------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+------------------+----------+--------------+------------------+
| mysql-bin.000123 |      111 | mydb         |                  |
+------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

<br>This is the position from which the slave database will start replicating. Record these numbers, they will come in useful later.

Step 9: Open new tap (Please keep the MySQL shell)
  >mysqldump -u root -p --opt mydb > mydb.sql
  <br> If you are using plesk 
  >mysqldump -uadmin -p`cat /etc/psa/.psa.shadow` --option mydb > mydb.sql

<br>

Step 10: After step 9 process completed go to the previous window (MySQL shell window)
<br>
>UNLOCK TABLES;
<br>
>quit;
