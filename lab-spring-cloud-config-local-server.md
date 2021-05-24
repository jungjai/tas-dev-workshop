# Building and Running Spring Cloud Config Server on your PC

optinally, you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-config/complate)

[spring cloud config reference doc](https://spring.io/projects/spring-cloud-config)


## Prerequisites
- [app prerequisites](lab-prerequisites-app.md)

## Create a git repo for application configuration.

some sample config repo.
- https://github.com/spring-cloud-samples/config-repo
- https://github.com/myminseok/spring-cloud-sample-configrepo



### 1. make a local directory named `spring-cloud-sample-configrepo`.
```
mkdir spring-cloud-sample-configrepo
```
### 2. create files
vi  spring-cloud-sample-configrepo/application.yml
```
cook.special: Kim-Chi(application.yml)
```

vi  spring-cloud-sample-configrepo/application-dev.yml
```
cook.special: "Kim-Chi(application-dev.yml)"
```

### 4. create a git repo
github.com/<YOUR-PROJECT>/spring-cloud-sample-configrepo.git

### 5. push to git repo
```
cd spring-cloud-sample-configrepo
git init
git add .
git commit -m "init"
git remote add origin git@github.com:<YOUR-PROJECT>/spring-cloud-sample-configrepo.git
git push -u origin master
```

## Create a spring cloud config server app
visit spring initializr https://start.spring.io to create a new Spring Boot project.

### 1. Build a app template on spring initializr 
While we could visit spring initializr https://start.spring.io to create a new Spring Boot project, we will start with a skeleton
- project: mvn or gradle
- language: java
- spring boot: 2.3.* : [app prerequisites](lab-prerequisites-app.md)
- project metadata> name: config-server
- project metadata> packaging: jar
- project metadata> java: 8
- dependencies: config-server

### 2. Download as zip 
### 3. Open a Terminal (e.g., _cmd_ or _bash_ shell)
### 4. Unzip 
### 5. Open this project in your editor/IDE of choice.


## Develop the app(config-server)
### 1. Add configuration(application.yml)
vi config-server/src/main/resources/application.yml
* In spring boot 2.3, file name can be bootstrap.yml or application.yml.
```
server.port: 8888
spring:
  cloud:
    config:
      name: configserver
      server.git:
        uri: https://github.com/<YOUR-PROJECT>/spring-cloud-sample-configrepo
        default-label: master

```
> add your git repo you created above.
> spring.cloud.config.server.git.default-label: make sure to the git branch you created above.

### 2. Enable spring cloud config server on spring boot application
add @EnableConfigServer annotation to ConfigServerApplication.java
```
package com.example.configserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigServerApplication.class, args);
	}

}

```

### 3. Run spring cloud config server locally.
Now we're ready to run the application
```
  gradle bootRun

  mvn spring-boot:run
```


### 4. Testing the SCS for Locating Remote Configuration Resources
The Config Service serves property sources from /{application}/{profile}/{label}, where the default bindings in the client app are as follows:

```
http://localhost:8888//{application}/{profile}

```
> "name" = ${spring.application.name}
> "profile" = ${spring.profiles.active} (actually Environment.getActiveProfiles())

[official docs](https://cloud.spring.io/spring-cloud-config/reference/html/#_locating_remote_configuration_resources)

for example,

```
curl http://localhost:8888/default/default
```

results:
```
{
  "name": "default",
  "profiles": [
    "default"
  ],
  "label": null,
  "version": "417992242418bff56a6deb3e4e6e8c29b8ae688b",
  "state": null,
  "propertySources": [
    {
      "name": "https://github.com/myminseok/spring-cloud-sample-configrepo/application.yml",
      "source": {
        "cook.special": "Kim-Chi(application.yml)"
      }
    }
  ]
}
```
check the config for `cloud-native-spring` app with `dev` profile
```
curl http://localhost:8888/cloud-native-spring/dev | jq
 

 {
  "name": "cloud-native-spring",
  "profiles": [
    "dev"
  ],
  "label": null,
  "version": "3ee1f128a23d6706e4dde8c9c6dc603a426b6c10",
  "state": null,
  "propertySources": [
    {
      "name": "https://github.com/myminseok/spring-cloud-sample-configrepo/application-dev.yml",
      "source": {
        "cook.special": "Kim Chi(git-repo> application-dev.yml)"
      }
    },
    {
      "name": "https://github.com/myminseok/spring-cloud-sample-configrepo/cloud-native-spring.yml",
      "source": {
        "cook.special": "Kim Chi(git-repo> cloud-native-spring.yml)"
      }
    },
    {
      "name": "https://github.com/myminseok/spring-cloud-sample-configrepo/application.yml",
      "source": {
        "cook.special": "Kim Chi(git-repo> application.yml)"
      }
    }
  ]
}

```

*The End of this lab*

---

## Reference
https://cloud.spring.io/spring-cloud-config/reference/html/#_locating_remote_configuration_resources

cook app:  https://github.com/spring-cloud-samples/cook

spring boot 2.4: https://spring.io/blog/2020/08/14/config-file-processing-in-spring-boot-2-4

reference workshop: https://github.com/Tanzu-Solutions-Engineering/devops-workshop/blob/master/labs/04-adding_spring_cloud_config_to_boot_application.adoc


