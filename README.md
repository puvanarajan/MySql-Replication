# MySqlReplication

######This is small tutorial for MySql replication

>Here I am using three servers.

Master Server A : **192.168.1.10** <br>
Slave server B  : **192.168.1.11** <br>
Slave server C  : **192.168.1.12** <br>

Step 1: Install the mysql server<br>
  >yum install mysql-server <br>
  >chkconfig mysqld on
<br>

Step 2: We need to restart the MySql server <br>
  >service mysqld start
<br>

Step 3: After the mysqld service has been started, set your mysql server root password by using following commands <br>
  >sudo /usr/bin/mysql_secure_installation

