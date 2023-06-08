# Installation Of OpenSRP in Ubuntu Server

## Requirements
- Ubuntu 18.04
- JAVA 8 and Java 11
- Mysql 5.6
- Postgres Database
- Redis Server
- Maven
- OpenMRS
- OpenSRP Server

Make sure to have installed JAVA 1.8 and Java 11 as required because OpenMRS and OpenSRP depend on those two versions of JAVA.


## 1. JAVA INSTALLATION

### Install JAVA 11
Please refer to this link to install JAVA 11 and JAVA 1.8 respectivelly. 
> For JAVA 11 you can also [click here](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04)

The easiest option for installing Java is to use the version packaged with Ubuntu. By default, Ubuntu 18.04 includes Open JDK 11, which is an open-source variant of the JRE and JDK.

To install this version, first update the package index:
```
sudo apt update
```
Next, check if Java is already installed:
```
java -version
```
If Java is not currently installed, you’ll see the following output:
```
Output
Command 'java' not found, but can be installed with:

sudo apt install default-jre
sudo apt install openjdk-11-jre-headless
sudo apt install openjdk-8-jre-headless
```
Execute the following command to install the default Java Runtime Environment (JRE), which will install the JRE from OpenJDK 11:
```
sudo apt install default-jre
```
The JRE will help you run almost all Java softwares.

Verify the installation with the following command:
```
java -version
```
You’ll see output similar to the following:
```
Output
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.18.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.18.04, mixed mode, sharing)
```
You may need the Java Development Kit (JDK) in addition to the JRE in order to compile and run some specific Java-based software. To install the JDK, execute the following command, which will also install the JRE:
```
sudo apt install default-jdk
```
Verify that the JDK is installed by checking the version of javac, the Java compiler:
```
javac -version
```
You’ll see the following output:
```
Output
javac 11.0.11
```

### Install JAVA 8
> For JAVA 8 installation you can also [click here](https://sysadminxpert.com/how-to-install-java-8-on-ubuntu-20/). 

Here are steps to follow when installing JAVA 8
```
apt-get install openjdk-8-jdk -y
```
Once Java is installed, verify the Java version using the following command:
```
java -version
```
You will get the following output:
```
openjdk version "1.8.0_312"
OpenJDK Runtime Environment (build
1.8.0_312-8u312-b07-0ubuntu1~20.04-b07)
OpenJDK 64-Bit Server VM (build 25.312-b07, mixed mode)
```

After installation is done you can always change JAVA version of focus using the following command

```
sudo update-alternatives --config java
```

This will show java versions installed and an asterisk to version being used such as follows: 
```
There are 2 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                            Priority   Status
------------------------------------------------------------
  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode
* 1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode
  2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode

Press <enter> to keep the current choice[*], or type selection number: 2

```

Now that you are done with JAVA Installation let's move to the next step.

## 2. MySQL 5.6 INSTALLATION

This is essential for OpenMRS as it supports MySQL 5.6 by default. To start installation follow the following instructions: 

First, create a user and group for MySQL using the following command:
```
groupadd mysql
useradd -g mysql mysql
```
Next, download MySQL 5.6 source with the following command:
```
wget
https://dev.mysql.com/get/Downloads/MySQL-5.6/mysql-5.6.46-linux-glib
c2.12-x86_64.tar.gz
```
Once the MySQL is downloaded, extract it with the following command:
```
tar -xvf mysql-5.6.46-linux-glibc2.12-x86_64.tar.gz
```
Next, move the extracted directory to /usr/local with the following command:
```
mv mysql-5.6.46-linux-glibc2.12-x86_64 /usr/local/mysql
```
Next, navigate to the /usr/local directory and set proper ownership with the following commands:
```
cd /usr/local/mysql
chown -R mysql:mysql *
```
Next, install the required dependencies using the following command:
```
apt-get install libaio1 libncurses5 libnuma-dev -y
```
Next, install MySQL with the following command:
```
scripts/mysql_install_db --user=mysql
````
Next, set proper ownership to MySQL directory and data directory:
```
chown -R root .
chown -R mysql data
```
Copy the mysql configuration file
```
sudo cp support-files/my-default.cnf /etc/my.cnf
```
Start mysql
```
sudo bin/mysqld_safe --user=mysql &
sudo cp support-files/mysql.server /etc/init.d/mysql.server
```
Set root user password
```
sudo bin/mysqladmin -u root password '[your new password]'
```
Add mysql path to the system
```
sudo ln -s /usr/local/mysql/bin/mysql /usr/local/bin/mysql
```
Reboot!

Start mysql server
```
sudo /etc/init.d/mysql.server start
```
Stop mysql server
```
sudo /etc/init.d/mysql.server stop
```
Check status of mysql
```
sudo /etc/init.d/mysql.server status
```
Enable mysql on startup
```
sudo update-rc.d -f mysql.server defaults
```
Check to see if mysql is running using the following command:
```
sudo systemctl status mysql.service
```
If mysql service is not running, use the following command to start:
```
sudo systemctl start mysql.service
```
Now login using below command
```
mysql -u root -p
```

## 3. OpenMRS INSTALLATION
First, create OpenMRS user using the following command: 
```
groupadd openmrs
useradd -m openmrs -g openmrs
```
Move to the OpenMRS directory
```
cd /home/openmrs
```
### Install Tomcat
Next, download the Tomcat 7 with the following command:
```
wget
https://archive.apache.org/dist/tomcat/tomcat-7/v7.0.109/bin/apache-tomcat7.0.109.tar.gz
```
Next, create a directory for Tomcat and extract the downloaded file to
tomcat-openmrs directory:
```
mkdir /tomcat-openmrs
tar -xvzf apache-tomcat-7.0.109.tar.gz -C tomcat-openmrs/
--strip-components=1
```
Next, navigate to the tomcat-openmrs directory and set proper permission and
ownership:
```
cd tomcat-openmrs
chgrp -R openmrs tomcat-openmrs
chmod -R g+r conf
chmod g+x conf
chown -R openmrs webapps/ work/ temp/ logs/
```

Next, you will need to create a system service file to manage the Tomcat service. You
can create it with the following command:
```
nano /etc/systemd/system/tomcat-openmrs.service
```
Add the following lines:
```
[Unit]
Description=OpenMRS Apache Tomcat Web Application Container
After=network.target
[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
Environment=CATALINA_PID=/home/openmrs/tomcat-openmrs/temp/tomcat.pid
Environment=CATALINA_HOME=/home/openmrs/tomcat-openmrs
Environment=CATALINA_BASE=/home/openmrs/tomcat-openmrs
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
ExecStart=/home/openmrs/tomcat-openmrs/bin/startup.sh
ExecStop=/home/openmrs/tomcat-openmrs/bin/shutdown.sh
User=openmrs
Group=openmrs
UMask=0007
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target
```
Save and close the file then reload the systemd daemon to apply the changes:
```
systemctl daemon-reload
```
Next, start the Tomcat service with the following command:
```
systemctl start tomcat-openmrs
```
You can now check the status of the Tomcat service with the following command:
```
systemctl status tomcat-openmrs
```
You will get the following output:
```
● tomcat.service - Apache Tomcat Web Application Container
Loaded: loaded (/etc/systemd/system/tomcat.service; disabled;
vendor preset: enabled)
Active: active (running) since Wed 2022-02-23 08:48:12 UTC; 6s
ago
Process: 10027 ExecStart=/opt/tomcat/bin/startup.sh (code=exited,
status=0/SUCCESS)
Main PID: 10049 (java)
Tasks: 28 (limit: 4686)
Memory: 94.1M
CGroup: /system.slice/tomcat.service
└─10049 /usr/lib/jvm/java-1.8.0-openjdk-amd64/bin/java
-Djava.util.logging.config.file=/opt/tomcat/conf/logging.properties
-Djav>
Feb 23 08:48:12 ubuntu2004 systemd[1]: Starting Apache Tomcat Web
Application Container...
Feb 23 08:48:12 ubuntu2004 startup.sh[10027]: Tomcat started.
Feb 23 08:48:12 ubuntu2004 systemd[1]: Started Apache Tomcat Web
Application Container.
```
At this point, Tomcat is started and listens on port 8080.

### Install OpenMRS
First, create a directory for OpenMRS and set proper ownership with the following command:
```
mkdir /var/lib/OpenMRS
chown -R openmrs:openmrs /var/lib/OpenMRS
```
Next, download the latest version of OpenMRS using the following command:
```
wget
https://sourceforge.net/projects/openmrs/files/releases/OpenMRS_Platf
orm_2.1.4/openmrs.war
```
Once the download is completed, copy the downloaded file to the Tomcat webapps directory:
```
cp openmrs.war /home/openmrs/tomcat-openmrs/webapps/
```
Next, change the ownership of the openmrs.war file to tomcat:
```
chown -R openmrs:openmrs /home/openmrs/tomcat-openmrs/webapps/openmrs.war
```
Open your web browser and access the OpenMRS web installation wizard using the URL http://your-server-ip:8080/openmrs

### Install OpenMRS Modules
Copy the OpenMRS modules from OpenMRS Modules directory, then add them into ```/home/openmrs/.OpenMRS/modules``` then restart OpenMRS server using the following command:
```
systemctl restart tomcat-openmrs
```
Get back to the browser and try to access openmrs again with new UI.
## 4. POSTGRESQL INSTALLATION
Install the Postgres package along with a -contrib package that adds some additional utilities and functionality:
```
sudo apt install postgresql postgresql-contrib
```
Ensure that the server is running by using the systemctl start command:
```
sudo systemctl start postgresql.service
```
Switch over to the postgres account on your server by typing:
```
sudo -i -u postgres
```
You can now access the PostgreSQL prompt immediately by typing:
```
psql
```
From there, you are free to interact with the database management system as necessary.
Exit out of the PostgreSQL prompt by typing:
```
\q
```
Create a postgres user for opensrp by typing the following, If you are logged in as the postgres account, you can create a new user by typing:
```
createuser --interactive
```
The script will prompt you with some choices and, based on your responses, execute the correct Postgres commands to create a user to your specifications.
```
Output
Enter name of role to add: opensrp
Shall the new role be a superuser? (y/n) y
```
Next creating opensrp database by typing the following
```
createdb opensrp
```
Adding user role opensrp to opensrp database
```
sudo -i -u opensrp
psql
```
Incase you get Postgres authentication errors use the following commands to update Postgres user password
```
sudo -u postgres psql
ALTER USER opensrp PASSWORD 'OpenSrpPostPass';
\q
```

## 5. REDIS SERVER INSTALLATION
Install Redis by typing the following command:
```
sudo apt install redis-server
```
Open this file with your preferred text editor:
```
sudo nano /etc/redis/redis.conf
```
Inside the file, find the supervised directive. This directive allows you to declare an init system to manage Redis as a service, providing you with more control over its operation. The supervised directive is set to no by default. Since you are running Ubuntu,
which uses the systemd init system, change this to systemd:
```
# If you run Redis from upstart or systemd, Redis can interact with your
# supervision tree. Options:
# supervised no - no supervision interaction
# supervised upstart - signal upstart by putting Redis into SIGSTOP mode
# supervised systemd - signal systemd by writing READY=1 to
$NOTIFY_SOCKET
# supervised auto - detect upstart or systemd method based on
# UPSTART_JOB or NOTIFY_SOCKET environment variables
# Note: these supervision methods only signal "process is ready."
# They do not enable continuous liveness pings back to your
supervisor.
supervised systemd
. . .
```
That’s the only change you need to make to the Redis configuration file at this point, so save and close it when you are finished. If you used nano to edit the file, do so by pressing CTRL + X, Y, then ENTER.

Then, restart the Redis service to reflect the changes you made to the configuration file:
```
sudo systemctl restart redis.service
```
Testing redis by checking if it is running by typing the following
```
sudo systemctl status redis
```
If it is running without any errors, this command will produce output similar to the following:
```
Output
● redis-server.service - Advanced key-value store
Loaded: loaded (/lib/systemd/system/redis-server.service; enabled;
vendor preset: enabled)
Active: active (running) since Thu 2020-04-30 23:26:54 UTC; 4s ago
Docs: http://redis.io/documentation,
man:redis-server(1)
Process: 36552 ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
(code=exited, status=0/SUCCESS)
Main PID: 36561 (redis-server)
Tasks: 4 (limit: 2345)
Memory: 1.8M
CGroup: /system.slice/redis-server.service
└─36561 /usr/bin/redis-server 127.0.0.1:6379
. . .
```
To test that Redis is functioning correctly, connect to the server using redis-cli, Redis’s command-line client:
```
redis-cli
```
In the prompt that follows, test connectivity with the ping command:
```
ping
```
```
Output
PONG
```
This output confirms that the server connection is still alive. Next, check that you’re able to set keys by running:
```
set test "It's working!"
```
```
Output
OK
```
Retrieve the value by typing:
```
get test
```
Assuming everything is working, you will be able to retrieve the value you stored:
```
Output
"It's working!"
```
After confirming that you can fetch the value, exit the Redis prompt to get back to the shell:
```
exit
```
As a final test, we will check whether Redis is able to persist data even after it’s been stopped or restarted. To do this, first restart the Redis instance:
```
sudo systemctl restart redis
```
Then connect with the command-line client again:
```
redis-cli
```
And confirm that your test value is still available
```
get test
```
The value of your key should still be accessible:
```
Output
"It's working!"
```
Exit out into the shell again when you are finished:
```
exit
```
Binding to localhost
To correct this, open the Redis configuration file for editing:
```
sudo nano /etc/redis/redis.conf
```
Locate this line and make sure it is uncommented (remove the # if it exists):
```
bind 127.0.0.1 :: 1
```
Save and close the file when finished (press CTRL + X, Y, then ENTER).
Then, restart the service to ensure that systemd reads your changes:
```
sudo systemctl restart redis
```
Configuring a Redis Password
Configuring a Redis password enables one of its two built-in security features—the auth command, which requires clients to authenticate to access the database. The password is configured directly in Redis’s configuration file, /etc/redis/redis.conf, so
open that file again with your preferred editor:
```
sudo nano /etc/redis/redis.conf
```
Scroll to the SECURITY section and look for a commented directive that reads:
```
# requiredpass foobared
```
Uncomment it by removing the #, and change foobared to a secure password.
After setting the password, save and close the file, then restart Redis:
```
sudo systemctl restart redis.service
```
To test that the password works, open up the Redis client:
```
redis-cli
```
The following shows a sequence of commands used to test whether the Redis password works. The first command tries to set a key to a value before authentication:
```
set key1 10
```
That won’t work because you didn’t authenticate, so Redis returns an error:
```
Output
(error) NOAUTH Authentication required.
```
The next command authenticates with the password specified in the Redis
configuration file:
```
auth your_redis_password
```
Redis acknowledges:
```
Output
OK
```
After that, running the previous command again will succeed:
```
set key1 10
```
```
Output
OK
```
get key1 queries Redis for the value of the new key.
```
get key1
```
```
Output
"10"
```
After confirming that you’re able to run commands in the Redis client after authenticating, you can exit redis-cli:
```
quit
```

## 6. INSTALL OpenSRP
Creating a user and usergroup for Opensrp by typing the following:
```
groupadd opensrp
useradd -m opensrp -g opensrp
```
Next, download the Tomcat 9 with the following command:
```
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
```
Next, create a directory for Tomcat and extract the downloaded file to /opt/tomcat directory:
```
mkdir /home/opensrp/tomcat-opensrp
tar -xvzf apache-tomcat-10.0.0.tar.gz -C /home/opensrp/tomcat-opensrp/
--strip-components=1
```
Next, navigate to the /home/opensrp/tomcat-opensrp directory and set proper permissions and ownership:
```
cd /home/opensrp/tomcat-opensrp
chgrp -R opensrp /home/opensrp/tomcat-opensrp
chmod -R g+r conf
chmod g+x conf
chown -R opensrp webapps/ work/ temp/ logs/
```
Create a Systemd Service File for Tomcat
Next, you will need to create a systemd service file to manage the Tomcat service. You can create it with the following command:
```
nano /etc/systemd/system/tomcat-opensrp.service
```
Add the following lines:
```
[Unit]
Description=OpenSRP Apache Tomcat Web Application Container
After=network.target
[Service]
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
Environment=CATALINA_PID=/home/opensrp/tomcat-opensrp/temp/tomcat.pid
Environment=CATALINA_HOME=/home/opensrp/tomcat-opensrp
Environment=CATALINA_BASE=/home/opensrp/tomcat-opensrp
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
ExecStart=/home/opensrp/tomcat-opensrp/bin/startup.sh
ExecStop=/home/opensrp/tomcat-opensrp/bin/shutdown.sh
User=opensrp
Group=opensrp
UMask=0007
RestartSec=10
Restart=always
[Install]
WantedBy=multi-user.target

```
Save the file and head on to change listening port for this tomcat because we already have OpenMRS tomcat running on default port.
Navigate and open server.xml file using the following command:
```
nano /home/opensrp/tomcat-opensrp/conf/server.xml
```
Update the lines containing the following information.
```
<Server port="8005" shutdown="SHUTDOWN">
```
and change 8005 to shutdown port you want.

Then change Connector tag

```
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000"
               />

```

update port="8000" and redirectPort="8443" to other ports that do not match ports used in openmrs-tomcat which can be seen from running the following command:
```
nano /home/openmrs/tomcat-openmrs/conf/server.xml
```

Once that is done save and close the file then reload the systemd daemon to apply the changes:
```
systemctl daemon-reload
```
Next, start the Tomcat service with the following command:
```
systemctl start tomcat-opensrp
```
You can now check the status of the Tomcat service with the following command:
```
systemctl status tomcat-opensrp
```
You will get the output resembling to the following:
```
● tomcat.service - OpenSRP Apache Tomcat Web Application Container
Loaded: loaded (/etc/systemd/system/tomcat.service; disabled; vendor
preset: enabled)
Active: active (running) since Wed 2022-02-23 08:48:12 UTC; 6s ago
Process: 10027 ExecStart=/home/opensrp/tomcat-opensrp/bin/startup.sh
(code=exited, status=0/SUCCESS)
Main PID: 10049 (java)
Tasks: 28 (limit: 4686)
Memory: 94.1M
CGroup: /system.slice/tomcat.service
└─10049 /usr/lib/jvm/java-1.8.0-openjdk-amd64/bin/java
-Djava.util.logging.config.file=/home/opensrp/tomcat-opensrp/conf/logging.p
roperties -Djav>
Feb 23 08:48:12 ubuntu2004 systemd[1]: Starting Apache Tomcat Web
Application Container...
Feb 23 08:48:12 ubuntu2004 startup.sh[10027]: Tomcat started.
Feb 23 08:48:12 ubuntu2004 systemd[1]: Started Apache Tomcat Web
Application Container.
```
## 7. INSTALL MAVEN
Installing Maven by type the following
```
sudo apt-get install maven
```

## 8. INSTALL OPENSRP SERVER
Clone the appropriate repository from [here](https://github.com/opensrp/opensrp-server-web.git) using the following command:

```
git clone https://github.com/opensrp/opensrp-server-web.git
```

from github page of the repository. Navigave to ```Releases``` then open list of Releases and open Release ```v2.1.70.10-APLHA4-SNAPSHOT```
or [click here](https://github.com/opensrp/opensrp-server-web/releases/tag/v2.1.70.10-ALPHA4-SNAPSHOT) to go straight, then copy the last commit which should be ```86c675f```

We need to make this commit the HEAD commit and create branch from it To do that to that commit using the following command:
```
git checkout 86c675f
```
After that, create a new branch using the following command:

```
 git checkout -b v2.1.70.10-APLHA4-SNAPSHOT
```
verify that you are in the created branch using the following command:
```
git branch -a
```
the output should look like the following (```* v2.1.70.10-APLHA4-SNAPSHOT```) is the current branch we are in:
```diff
master
+ * v2.1.70.10-APLHA4-SNAPSHOT
  remotes/origin/1172-2.1-allow-search-via-post
  remotes/origin/1172-master-allow-search-via-post
  remotes/origin/1172-v2-allow-search-via-post
  remotes/origin/1174-hsts-security-header-missing
  remotes/origin/125-API-for-view-page
  remotes/origin/2.1.24-alpha-SNAPSHOT
  remotes/origin/262-Plan-Identifiers-Endpoint
  remotes/origin/344-use-form-names-as-identifiers
  remotes/origin/350-implement-acl-caching-work
  remotes/origin/502-allow-sync-of-global-settings-separately
  remotes/origin/558-extend-stock-api
  remotes/origin/561-add-api-on-location
  remotes/origin/594-set-photourl-in-product-catalogue
  remotes/origin/607-paginate-get-stocks-by-service-point-id
...
```
then install the submodules of this repository using the following command:

```
git submodule update --init --recursive
```

Once we are done with updating the submodules of the repository. It's time that we set our configurations before building our application.

Configure OpenMRS and Redis settings in opensrp.properties.
From the root folder of the project type the following command:
```
nano config/assets/config/opensrp.properties
```
After opening the file, set OpenMRS settings and Redis configurations as required. Save and close the file.

Then configure database settings in context.xml file.
From the root folder of the project type the following command:
```
nano src/main/webapp/META-INF/context.xml
```
Then update database configurations accordingly.
```

<Context>
<Resource
                name="jdbc/openSRPDB"
                auth="Container"
                type="javax.sql.DataSource"
                factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
                initialSize="5"
                maxActive="55"
                maxIdle="21"
                minIdle="13"
                timeBetweenEvictionRunsMillis="34000"
                minEvictableIdleTimeMillis="55000"
                validationQuery="SELECT 1"
                validationInterval="34"
                testOnBorrow="true"
                removeAbandoned="true"
                removeAbandonedTimeout="233"
                username="<username>"
                password="<password>"
                driverClassName="org.postgresql.Driver"
                url="jdbc:postgresql://<IP Adrr>:<Port>/<database name>"
     />
</Context>
```
Replace all <> and text inside with corrent values.

Once that is done, use maven installed to build the project using the following command:
```
mvn clean install -Dmaven.test.skip=true
```

After finishing to build, you'll find ```opensrp.war``` into ```target``` folder.

From root folder of the project, copy that folder and paste it into opensrp tomcat server using the following command:
```
cp /target/opensrp.war /home/opensrp/opensrp-tomcat/webapp/
```

After that wait for it to be reflected inside tomcat and open your browser and access it using  ```http://ip-address:port/opensrp```

Once you see white screen with test **Welcome to OpenSRP**.

then you are ready to go for OpenSRP Client as the server is running.

