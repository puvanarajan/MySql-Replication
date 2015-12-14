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

Step 4: Server we need give the port accessing permission. By default MySql server is using port 3306. So we need to add this port iptable.<br> 
NOTE: If you change the default port, then you have to add the default port to iptables.<br>
  >iptables -I RH-Firewall-1-INPUT 10 -p tcp --dport 3306 -j ACCEPT <br>
  >service iptables save 
