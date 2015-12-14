# MySqlReplication

######This is small tutorial for MySql replication

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
  >vi /etc/my.cnf <br> and check following lines
  <br>
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

