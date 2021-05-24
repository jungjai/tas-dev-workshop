
# Adding spring cloud config client on your app
we will re-use the app from [previous lab: building-spring-boot-app-config](lab-developing-spring-boot-app-config.md)

optinally, you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-config/complate)

### Prerequisites
- [app from previous lab](lab-developing-spring-boot-app-config.md)
- we will re-use the app from [previous lab: building-spring-boot-app-config](lab-developing-spring-boot-app-config.md)


## Add library
### 1. app template 
put the same param on spring initializr https://start.spring.io with additional dependencies
- project: mvn
- language: java
- spring boot: 2.3.* : [app prerequisites](lab-prerequisites-app.md)
- project metadata> name: cloud-native-spring
- project metadata> packaging: jar
- project metadata> java: 8
- dependencies: web, config-client(TAS), security, spring-boot-actuator
> > make sure to set dependencies as above

### 2. click `EXPLORER CTRL+SPACE` button and `copy` button.
### 3. paste the dependencies to the pom.xml 
check the changes. reference to https://docs.pivotal.io/spring-cloud-services/3-1/common/client-dependencies.html

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
  ...

```

### bootstrap.yml
set spring.cloud.config.uri in bootstrap.yml. Add the spring config server info to cloud-native-spring/src/main/resources/bootstrap.yml.reference to https://cloud.spring.io/spring-cloud-config/multi/multi__spring_cloud_config_client.html

```
spring:
  cloud:
    config:
      uri: http://localhost:8888
      fail-fast: true
```
> As long as Spring Boot Actuator and Spring Config Client are on the classpath any Spring Boot application will try to contact a config server on http://localhost:8888, the default value of spring.cloud.config.uri.
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

### Disable HTTP Basic Authentication

The Spring Cloud Config Client starter has a dependency on Spring Security. Unless your app has other security configuration, this will cause all app endpoints to be protected by HTTP Basic authentication. you can disable by overriding WebSecurityConfigurerAdapter. add src/main/java/com/example/cloudnativespring/SecurityConfiguration.java
```

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@Profile({"dev","cloud","prod"})
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

## Build the application

### Next we'll package the application as an executable artifact (that can be run on its own because it will include all transitive dependencies along with embedding a servlet container)
```
  mvn clean package -DskipTests
```

## Run and Test the application

Now we're ready to run the application
```
  mvn clean spring-boot:run


 .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::       (v2.3.11.RELEASE)

2021-06-04 18:44:59.897  INFO 21154 --- [           main] c.c.c.ConfigServicePropertySourceLocator : Fetching config from server at : http://localhost:8888
2021-06-04 18:45:00.511  INFO 21154 --- [           main] c.c.c.ConfigServicePropertySourceLocator : Located environment: name=cloud-native-spring, profiles=[default], label=null, version=80ed78226fb8bcba2612c2118bff29c7bdf1d0bb, state=null
2021-06-04 18:45:00.512  INFO 21154 --- [           main] b.c.PropertySourceBootstrapConfiguration : Located property source: [BootstrapPropertySource {name='bootstrapProperties-configClient'}, BootstrapPropertySource {name='bootstrapProperties-https://github.com/myminseok/spring-cloud-sample-configrepo/application.yml'}]
2021-06-04 18:45:00.517  INFO 21154 --- [           main] c.e.c.CloudNativeSpringApplication       : The following profiles are active: dev1
2021-06-04 18:45:00.995  INFO 21154 --- [           main] o.s.cloud.context.scope.GenericScope     : BeanFactory id=6f83338b-ffd3-3fb7-bda7-4b7076d95f8c
2021-06-04 18:45:01.176  INFO 21154 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-06-04 18:45:01.185  INFO 21154 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-06-04 18:45:01.185  INFO 21154 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.46]
2021-06-04 18:45:01.243  INFO 21154 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-06-04 18:45:01.243  INFO 21154 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 717 ms
```
> check the configuraton file list
> check the active profile

### Testing the application
```
curl https://localhost:8080/menu

Today's special is: Kim Chi(git-repo> application.yml)
```
Stop the application. In the terminal window type *Ctrl + C*

### Testing the application with different profile
re-run app with `dev` profile.

```
mvn clean spring-boot:run -Dspring-boot.run.profiles=dev

curl https://localhost:8080/menu

Today's special is: Kim Chi(git-repo> application-dev.yml)
```


## Modifying the configuration on git repo

### 1. change file on config repo.
change 'application-dev.yml' on github.com/<YOUR-PROJECT>/spring-cloud-sample-configrepo.git

### 2. refresh client app  using actuator
```
curl -X POST http://localhost:8080/actuator/refresh

["cook.special"]
```

### 3. check configuraton changes on client app.
```
curl https://localhost:8080/menu

```
Stop the application. In the terminal window type *Ctrl + C*


*The End of this lab*

---