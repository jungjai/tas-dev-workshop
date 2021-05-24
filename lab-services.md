# Cloud Foundry Service Broker
https://docs.pivotal.io/application-service/2-10/services/index.html

# service managed by cloud foundry platform

### cf marketplace
After targeting and logging into Cloud Foundry, run the cf marketplace cf CLI command to view the services available to your targeted organization. Available services may differ between organizations and between Cloud Foundry marketplaces

### cf create-service 
service broker will create an service instance under the org,space. the service instance can be VM, or app which is desiged by the service author.

### cf bind-service
service broker will create access key(id/password) to the service instance. and inject the key to application container as environment variable.

### cf create-service-key
cf bind-service will create a new id/password for the service instance when binding and unbinding. on the other hand, you can create another service access key(id,password) which is not changed(static)


# service instance outside of cloud foundry platform

### Creating User-Provided Service Instances
https://docs.pivotal.io/application-service/2-10/devguide/services/user-provided.html
you can use `user provided service` to access from app to the external service instance which is not managed by cloud foundary platform. In other words, Cloud Foundry also allows service connection information and credentials to be provided by a user. In order for the application to detect and connect to a user-provided service, a single `uri` field should be given in the credentials using the form `<dbtype>://<username>:<password>@<hostname>:<port>/<databasename>`.

These steps use examples for username, password, host name, and database name that should be replaced with real values.

```
# create a user-provided Oracle database service instance
$ cf create-user-provided-service oracle-db -p '{"uri":"oracle://root:secret@dbserver.example.com:1521/mydatabase"}'
# create a user-provided MySQL database service instance
$ cf create-user-provided-service mysql-db -p '{"uri":"mysql://root:secret@dbserver.example.com:3306/mydatabase"}'
# bind a service instance to the application
$ cf bind-service <app name> <service name>
# restart the application so the new service is detected
$ cf restart
```

### Creating Service Instance from Marketplace
https://docs.pivotal.io/application-service/2-10/devguide/services/managing-services.html

Let's create a MySQL database instance. Hopefully, you will have https://network.pivotal.io/products/pivotal-mysql[p.mysql] service available in CF Marketplace.

```
$ cf marketplace
Getting services from marketplace in org my-org / space test as user@example.com...
OK

service            plans               description                       broker
p-mysql            db-small          A DBaaS                           mysql-broker

$ cf marketplace -s p.mysql

Getting service plan information for service p.mysql as cphillipson@pivotal.io...
OK

service plan   description                                            free or paid
db-small       This plan provides a small dedicated MySQL instance.   free
```

Let's create an instance of `p.mysql` with `db-small` plan, e.g.
```
$ cf create-service p.mysql db-small mysql-database

Creating service instance mysql-database in org zoo-labs / space development as cphillipson@pivotal.io...
OK


$ cf services
Getting services in org my-org / space test as user@example.com...
OK

name       service       plan        bound apps   last operation      broker                upgrade available
mydb       p-mysql       db-small                    create succeeded    mysql-broker          yes


```

### Binding service instance to a existing App
To test the application with different services, you can simply stop the app, unbind a service, bind a different database service, and start the app:

```
$ cf unbind-service <app name> <service name>
$ cf bind-service <app name> <service name>
$ cf restart
```

### Binding service instance while cf push

clone a sample app
```
git clone https://github.com/adamfowleruk/spring-music
```

Edit App manifest.yml
```
services:
 - mysql-database
```
deploy to the tAS
```
cf push
```


## Accessing mysql instance for troubleshooting

### 1. create service key on service instance

```
$ cf help -a | grep key
   create-service-key                     Create key for a service instance
   service-keys                           List keys for a service instance
   service-key                            Show service key info
   delete-service-key                     Delete a service key

$ cf create-service-key mysql-database test-key
Creating service key test-key for service instance mysql-database as minseok...
OK

$  cf service-key mysql-database test-key
Getting key test-key for service instance mysql-database as minseok...

{
 "hostname": "280cb9d7-c0fc-4006-9971-a5377c2bd2f2.mysql.service.internal",
 "jdbcUrl": "jdbc:mysql://280cb9d7-c0fc-4006-9971-a5377c2bd2f2.mysql.service.internal:3306/service_instance_db?user=46fc09f7a642438981498ce464190af3\u0026password=jnt7drl88lve16so\u0026useSSL=false",
 "name": "service_instance_db",
 "password": "jnt7drl88lve16so",
 "port": 3306,
 "uri": "mysql://46fc09f7a642438981498ce464190af3:jnt7drl88lve16so@280cb9d7-c0fc-4006-9971-a5377c2bd2f2.mysql.service.internal:3306/service_instance_db?reconnect=true",
 "username": "46fc09f7a642438981498ce464190af3"
}

```
copy `hostname`, `port`, `username`, `password`



### 3. ssh turnneling to gemfire cluster via app.
open another terminal and run following command. refer to [lab-cf-ssh](lab-cf-ssh.md)
```
$ cf ssh -L 3306:280cb9d7-c0fc-4006-9971-a5377c2bd2f2.mysql.service.internal:3306 cloud-native-spring

vcap@e8dd18f9-724d-4c2e-5d56-9d74:~$ 
```
now, turnnel to the gemfire cluster is ready on the terminal session 


### 4. install percona(mysql) cli
open another terminal and install percona(mysql) cli
- [guide](https://www.percona.com/doc/percona-server/8.0/installation/apt_repo.html)

```
## Install GnuPG, the GNU Privacy Guard:
$ sudo apt-get install gnupg2

##  Fetch the repository packages from Percona web:
$ wget https://repo.percona.com/apt/percona-release_latest.$(lsb_release -sc)_all.deb

##  Install the downloaded package with dpkg. To do that, run the following commands as root or with sudo:
$ sudo dpkg -i percona-release_latest.$(lsb_release -sc)_all.deb

##  Once you install this package the Percona repositories should be added. You can check the repository setup in the /etc/apt/sources.list.d/percona-release.list file.

Enable the repository:

$ sudo percona-release setup ps80

##  After that you can install the client package:
$ sudo apt-get install percona-server-client

```

### 5. access to mysql instance.
use `hostname`, `port`, `username`, `password` from the service key.
```
## check network connection
$ nc -zv localhost 3306
Connection to localhost 3306 port [tcp/mysql] succeeded!

##  access to mysql instance.
mysql -u <username> -h 0 -p -D service_instance_db -P <localport>

ubuntu@ubuntu18:~/workspace/spring-music$ mysql -u 46fc09f7a642438981498ce464190af3 -h 0 -p -D service_instance_db -P 3306
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 453
Server version: 5.7.32-35-log MySQL Community Server (GPL)

Copyright (c) 2009-2021 Percona LLC and/or its affiliates
Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 

```
*The End of this lab*

---
## Advanced Topics

### Managing Service Keys
cf bind-service will create a new id/password for the service instance. you can create another service access key(id,password) which is not changed(static)


### Update a Service Instance
By updating the service plan for an instance, users can change their service instance to other service plans. Though the platform and CLI now support this feature, services must expressly implement support for it so not all services will. Further, a service might support updating between some plans but not others. For instance, a service might support updating a plan where only a logical change is required, but not where data migration is necessary. In either case, users can expect to see a meaningful error when plan update is not supported
```
$ cf update-service mydb -p new-plan
Updating service instance mydb as user@example.com...
OK
```

### Upgrade a Service Instance
Some service brokers support upgrading service instances to the latest version of a service plan. For example, a broker may want to provide a way for users of the service to upgrade the underlying operating system that their service instances run on.
```
$ cf services
Getting services in org acceptance / space dev as admin...

name      service   plan     bound apps   last operation     broker         upgrade available
mydb      p-mysql   small                 create succeeded   mysql-broker   yes
otherdb   p-mysql   medium                create succeeded   mysql-broker   no

$ cf update-service mydb --upgrade
You are about to update mydb.
Warning: This operation may be long running and will block further operations on the service until complete.
Really update service mydb? [yN]: y
OK

```

### Database drivers

Database drivers for MySQL, Postgres, Microsoft SQL Server, MongoDB, and Redis are included in the project.

To connect to an Oracle database, you will need to download the appropriate driver (e.g. from http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html). Then make a `libs` directory in the `spring-music` project, and move the driver, `ojdbc7.jar` or `ojdbc8.jar`, into the `libs` directory.
In `build.gradle`, uncomment the line `compile files('libs/ojdbc8.jar')` or `compile files('libs/ojdbc7.jar')` and run `./gradle assemble`

### Parsing VCAP_SERVICES
Java CFEnv is a library for easily accessing the environment variables set when deploying an application to Cloud Foundry. https://github.com/pivotal-cf/java-cfenv/

*The End of this lab*

### References

https://github.com/adamfowleruk/spring-music
https://github.com/cloudfoundry-samples/spring-music
https://github.com/Tanzu-Solutions-Engineering/devops-workshop/edit/master/labs/02-adding_persistence_to_boot_application.adoc