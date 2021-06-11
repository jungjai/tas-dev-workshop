# Using Blue-Green Deployment to Reduce Downtime and Risk

Reference to [guide](https://docs.pivotal.io/application-service/2-10/devguide/deploy-apps/blue-green.html)

### Prerequisites
- we will re-use the app from [previous lab: building-spring-boot-app-config](lab-developing-spring-boot-app-config.md)


### 1. cf push `blue` application
manifest-blue.yml
``` 
applications:
  - name: cloud-native-spring-blue
    #random-route: true
    instances: 1
    path: ./target/cloud-native-spring-0.0.1-SNAPSHOT.jar
    buildpacks:
      - java_buildpack_offline
    routes:
    - route: service.example.com
```

```
cf push -f manifest-blue.yml
```



### 2. cf push `green` application
manifest-green.yml
``` 
applications:
  - name: cloud-native-spring-green
    #random-route: true
    instances: 1
    path: ./target/cloud-native-spring-0.0.1-SNAPSHOT.jar
    buildpacks:
      - java_buildpack_offline
    routes:
    - route: temp.example.com
```
push and test the app
```
cf push -f manifest-green.yml
```


### 3. switch routes

```
cf map-route cloud-native-spring-green example.com --hostname service
cf unmap-route cloud-native-spring-blue example.com --hostname service
cf stop cloud-native-spring-blue

```

now the cloud-native-spring-green is servicing from  `service.example.com`.


*Congratulations!* You’ve just completed your first Spring Boot application.

*The End of this lab*

---
