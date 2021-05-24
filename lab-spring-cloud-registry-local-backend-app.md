# Build a new backend application (greeter-messages)
optinally, you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-registry)

[spring cloud eureka reference doc](https://cloud.spring.io/spring-cloud-netflix/multi/multi__service_discovery_eureka_clients.html)

### Prerequisites
- [app prerequisites](lab-prerequisites-app.md)

## Create a new app
visit spring initializr https://start.spring.io to create a new Spring Boot project.

### 1. Build a app template on spring initializr 
While we could visit spring initializr https://start.spring.io to create a new Spring Boot project, we will start with a skeleton
- project: mvn or gradle
- language: java
- spring boot: 2.3.* : [app prerequisites](lab-prerequisites-app.md)
- project metadata> name: `greeter-messages`
- project metadata> packaging: jar
- project metadata> java: 8
- dependencies: `reactive web`, `security`,  `service-registry(TAS)`
> make sure to set dependencies

### 2. `GENERATE as zip`
### 3. Open a Terminal (e.g., _cmd_ or _bash_ shell)
### 4. Unzip 
```
unzip greeter-messages.zip
```
### 5. Open this project in your editor/IDE of choice.

## Develop the app

### 1. check the pom.xml

[official guide](https://docs.pivotal.io/spring-cloud-services/3-1/common/client-dependencies.html)

```
 ...
 <parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.11.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>greeter-messages</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>greeter-messages</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>1.8</java.version>
		<spring-cloud-services.version>2.3.0.RELEASE</spring-cloud-services.version>
		<spring-cloud.version>Hoxton.SR11</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-security</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>
		<dependency>
			<groupId>io.pivotal.spring.cloud</groupId>
			<artifactId>spring-cloud-services-starter-service-registry</artifactId>
		</dependency>
  ...

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


### 2. Enable Discovery Client
add @EnableDiscoveryClient annotation to GreeterMessagesApplication.java
```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;


@SpringBootApplication
@EnableDiscoveryClient
public class GreeterMessagesApplication {

	public static void main(String[] args) {
		SpringApplication.run(GreeterMessagesApplication.class, args);
	}

}


```
create MessagesController.java

```
package com.example.greetermessages;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class MessagesController {

	private Log log = LogFactory.getLog(MessagesController.class);

	@RequestMapping("/greeting")
	public Greeting greeting(@RequestParam(value = "salutation", defaultValue = "Hello") String salutation, @RequestParam(value = "name", defaultValue = "Bob") String name) {
		log.info(String.format("Now saying \"%s\" to %s", salutation, name));
		return new Greeting(salutation, name);
	}

}

class Greeting {

	private static final String template = "%s, %s!";

	private String message;

	public Greeting(String salutation, String name) {
		this.message = String.format(template, salutation, name);
	}

	public String getMessage() {
		return this.message;
	}

}

```


SecurityConfiguration.java 

```

package com.example.greetermessages;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Profile("dev")
@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http
				.authorizeRequests().anyRequest().permitAll()
				.and()
				.httpBasic().disable()
				.csrf().disable();
	}

}

```


### 3. Edit application.yml
Set the spring.application.name property in application.yml.

```
spring:
  profiles.active: dev
  application:
    name: greeter-messages

server.port: 9001

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:8761/eureka/


```



### 4. Run the app
Now we're ready to run the application
```
  gradle bootRun

  mvn spring-boot:run
```

### 5. Test the app on local PC
```
 curl http://localhost:9001/greeting\?salutation\=hi\&name\=J

{"message":"hi, J!"}%

```

## Deploy app on TAS

### Create an application manifest.yml
[app manifest reference](lab-cf-manifest.md) 

```
applications:
- name: greeter-messages
  random-route: true
  instances: 1
  path: ./build/libs/greeter-messages-1.0-SNAPSHOT.jar
  buildpacks: 
  - java_buildpack_offline
  stack: cflinuxfs3

  services:
  - greeter-service-registry

  env:
    TRUST_CERTS: api.cf.example.com
    SPRING_PROFILES_ACTIVE: dev
```

### Push application into Cloud Foundry
[lab-cf-push-app](lab-cf-push-app.md)
```
cf push -f manifest.yml
```

### Check Service Registry Dashboard.
the app should be registered on the service registry in a few second.

*The End of this lab*

---
