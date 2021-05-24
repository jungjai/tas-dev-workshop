# Running apps with the SCS(SpringCloudServices) Service Registry Instance on TAS

[official guide about Spring Cloud Service Registry on TAS](https://docs.pivotal.io/spring-cloud-services/3-1/common/service-registry/index.html)

you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-registry)

### Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- we will re-use the apps from [lab: lab-spring-cloud-registry-server-local](lab-spring-cloud-registry-server-local.md)

## Getting Started

### 1. Create a Service Registry instance on TAS

```
$ cf target -o myorg -s development

$ cf marketplace -s p.service-registry
Getting service plan information for service p.service-registry as user...
OK

service plan   description     free or paid
standard       Standard Plan   free

$ cf create-service p.service-registry standard greeter-service-registry

$ cf service service-registry
Showing info of service service-registry in org myorg / space development as user...

name:            greeter-service-registry
service:         p.service-registry
bound apps:
tags:

[...]

Showing status of last operation from service service-registry...

status:    create succeeded
```

### 2. Deploy the apps on TAS
check the app configuration in application.yml. Set the spring.application.name property. you can comment out all eureka config on TAS. 
- [deploy frontend app on TAS](lab-spring-cloud-registry-local-frontend-app.md]
```
spring:
  application:
    name: cloud-native-spring
  main:
    allow-bean-definition-overriding: true

#eureka:
#  client:
#    serviceUrl:
#      defaultZone: ${vcap.services.eureka-service.credentials.uri:http://127.0.0.1:8761}/eureka/

management:
  endpoints:
    web:
      exposure:
        include: '*'

cook.special: "Kim Chi in cloud-native-spring app"
...
```
> eureka.client.serverUrl.defaultZone: you can comment out all eureka config on TAS.
> server.port: no affect on TAS. always 8080

the same on the backend app.
- [deploy backend app on TAS](lab-spring-cloud-registry-local-backend-app.md]

```
spring:
  application:
    name: greeter-messages
```


### 3. Check Service Registry Dashboard.
the apps should be registered on the service registry in a few second.

### 4. Allow Container-to-Container Networking
Before a client app can use the Service Registry to reach this directly-registered app, you must add a network policy that allows traffic from the client app to this app.
https://docs.pivotal.io/spring-cloud-services/3-1/common/service-registry/writing-client-applications.html#consume-using-c2c

```
(optional)
$ cf network-policies
Listing network policies in org myorg / space dev as user...

source   destination   protocol   ports

$ cf add-network-policy cloud-native-spring --destination-app greeter-messages --protocol tcp --port 8080
Adding network policy to app greeter in org myorg / space dev as user...
OK

$ cf network-policies
Listing network policies in org myorg / space dev as user...

source    destination          protocol   ports
greeter   greeter-messages   tcp        8080
The Greeter app can now use container networking to access the Messages app via the Service Registry. For more information about configuring container networking, see Administering Container-to-Container Networking.
```

### 5. Testing the application app on TAS
```
$ curl -k  http://<FRONEND-APP-TAS-URL/hello\?salutation=HI\&name=John

Hi, John!
```

*The End of this lab*

---

## Reference
- https://github.com/Tanzu-Solutions-Engineering/devops-workshop/blob/master/labs/05-adding_service_registration_and_discovery_with_spring_cloud.adoc

- TODO: https://waveland.education.pivotal.io/cnd-course/cloud-native-developer/spring-cloud-developer/service-discovery/index.html