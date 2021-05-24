# Running apps with the SCS(SpringCloudServices) Config Server Instance on TAS

[official guide about Spring Cloud Config Server on TAS](https://docs.pivotal.io/spring-cloud-services/3-1/common/config-server/index.html)

Optinally, you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-config/complate)


### Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- we will re-use the app from [previous lab: ab-spring-cloud-config-client-app](lab-spring-cloud-config-client-app.md)


## Create a SCS Config Server Instance on TAS

[offical guide](https://docs.pivotal.io/spring-cloud-services/3-1/common/config-server/configuring-with-git.html)


```
$ cf target -o myorg -s development

$ cf marketplace -s p.config-server
Getting service plan information for service p.config-server as user...
OK

service plan   description       free or paid
standard       A standard plan   free


$ cf create-service -c '{ "git": { "uri": "https://github.com/myminseok/spring-cloud-sample-configrepo" }, "count": 1 }' p.config-server standard my-config-server

$ cf create-service -c '{ "git": { "uri": "https://github.com/myminseok/spring-cloud-sample-configrepo.git", "label": "master", "username": "YOUR GIT ID", "password": "YOUR-GIT-PASSWORD", "skipSslValidation": true}, "count": 1 }' p.config-server standard my-config-server


$ cf update-service my-config-server -c



$ cf service my-config-server
Showing info of service my-config-server in org myorg / space dev as user...

name:            my-config-server
service:         p.config-server
tags:
plan:            standard
[...]

Showing status of last operation from service my-config-server...

status:    create succeeded
```

## Develop the app(cloud-native-app)
we will re-use the app from [previous lab-spring-cloud-config-client-app](lab-spring-cloud-config-client-app.md)

### 1. Edit dependency(pom.xml)
[official guide](https://docs.pivotal.io/spring-cloud-services/3-1/common/client-dependencies.html)

```
 ...
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.11.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
  </parent>

	<properties>
		<java.version>1.8</java.version>
		<spring-cloud.version>Hoxton.SR11</spring-cloud.version>
		<spring-cloud-services.version>2.3.0.RELEASE</spring-cloud-services.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>io.pivotal.spring.cloud</groupId>
			<artifactId>spring-cloud-services-starter-config-client</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		  </dependency>
		<dependency>

	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
			<dependency>
				<groupId>io.pivotal.spring.cloud</groupId>
				<artifactId>spring-cloud-services-dependencies</artifactId>
				<version>${spring-cloud-services.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

```


### bootstrap.yml
on TAS, you can comment out spring.cloud.config.uri. TAS will automatically connect if you bind app with config server instance.

```
spring:
  cloud:
    config:
      #uri: ${vcap.services.my-config-server.credentials.uri:http://localhost:8888} 
      fail-fast: true
      
```
> fail-fast: In some cases, you may want to fail startup of a service if it cannot connect to the Config Server. If this is the desired behavior, set the bootstrap configuration property spring.cloud.config.fail-fast=true to make the client halt with an Exception


### application.yml
expose actuator endpoints on cloud-native-spring/src/main/resources/application.yml for development purpose only.

```
spring:
  application:
    name: cloud-native-spring

server.port: 8080

management:
  endpoints:
    web:
      exposure:
        include: '*'

cook.special: "Kim Chi in cloud-native-spring app"
```


### 3. Build the application
```
  mvn clean package -DskipTests
```


## Deploy the app on TAS

### 1. Create an application manifest
[app manifest reference](lab-cf-manifest.md). edit lab-spring-cloud-config/cloud-native-spring/manifest.yml

```
applications:
- name: cloud-native-spring
  random-route: true
  instances: 1
  path: ./build/libs/cloud-native-spring-1.0-SNAPSHOT.jar
  services:
  - my-config-server
  #env:
  #  SPRING_PROFILES_ACTIVE: prod
```
> 
    


### 2. Push application into Cloud Foundry
[lab-cf-push-app](lab-cf-push-app.md)
```
cf push

cf push -f manifest.yml
```

### 3. Testing the application app on TAS
changing git config and check the app. reference the test procecdure from the [previous lab-spring-cloud-config-client-app](lab-spring-cloud-config-client-app.md)

```
curl -k https://<APP-URL-TAS>/menu

Today's special is: Kim Chi(git-repo> application-cloud.yml)
```

check if the SPRING_PROFILES_ACTIVE is set.

```
applications:
- name: cloud-native-spring
  random-route: true
  instances: 1
  path: ./build/libs/cloud-native-spring-1.0-SNAPSHOT.jar
  services:
  - my-config-server
  env:
    SPRING_PROFILES_ACTIVE: prod
```

```
cf push

curl -k https://<APP-URL-TAS>/menu

Today's special is: Kim Chi(git-repo> application-prod.yml)
```

*The End of this lab*

---

## Additional Resources
- (optional) cf plugin: https://github.com/pivotal-cf/spring-cloud-services-cli-plugin/blob/main/docs/cli.md. https://plugins.cloudfoundry.org/#spring-cloud-services

```
 cf install-plugin ./spring-cloud-services-cli-plugin-linux-amd64-1.0.22 

 cf config-server-sync-mirrors my-config-server
Successfully refreshed mirrors

cf scs-stop my-config-server

cf scs-start my-config-server

cf scs-restart my-config-server



```
TODO using plugin
