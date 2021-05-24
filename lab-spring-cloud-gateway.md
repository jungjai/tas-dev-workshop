# Running apps with the SCG(Spring cloud gateway) Instance on TAS

[official guide about Spring cloud gateway on TAS](https://docs.pivotal.io/spring-cloud-gateway/1-1//getting-started.html)


### Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- we will re-use the app from [previous lab: lab-spring-cloud-registry-local-frontend-app](lab-spring-cloud-registry-local-frontend-app.md)


## Create a Spring cloud gateway Instance on TAS

[offical guide](https://docs.pivotal.io/spring-cloud-gateway/1-1/managing-service-instances.html)


### create a gateway service with domain url: mygateway.apps.data.kr
```
$ cf target -o myorg -s development

$ cf create-service p.gateway standard my-gateway -c '{ "host": "mygateway", "domain": "apps.data.kr" }'

```

find dashboard url and access to the dashboard.

```
cf service my-gateway

Showing info of service my-gateway in org EDU01 / space TEST as minseok...

name:             my-gateway
service:          p.gateway
tags:             
plan:             standard
description:      Spring Cloud Gateway for VMware Tanzu
documentation:    
dashboard:        https://mygateway.apps.data.kr/scg-dashboard
service broker:   scg-service-broker

Showing status of last operation from service my-gateway...

status:    create succeeded
message:   create service instance completed
started:   2021-06-07T02:22:59Z
updated:   2021-06-07T02:25:09Z

bound apps:

```

## Testing the gateway.

bind an app with the gateway. and check on the dashboard.
```

cf bind-service cloud-native-spring my-gateway -c '{ "routes": [ { "path": "/menu/**" } ] }'


curl -k https://mygateway.apps.data.kr/menu

```

check the output and modify the instance. check the options from [the guide](https://docs.pivotal.io/spring-cloud-gateway/1-1/configuring-routes.html)

```
cf unbind-service cloud-native-spring my-gateway

cf bind-service cloud-native-spring my-gateway -c '{ "routes": [ { "path": "/menu/**" ,"filters": ["StripPrefix=0"] } ] }'

curl -k https://mygateway.apps.data.kr/menu
```

bind another apps to the gateway.

```
$ curl -k https://greeter.apps.data.kr/hello?salutation=HelloAgain!

cf bind-service greeter my-gateway -c '{ "routes": [ { "path": "/hello/**","filters": ["StripPrefix=0"] } ] }'


$ curl -k https://mygateway.apps.data.kr/hello?salutation=HelloAgain!

```

Cross-Origin Resource Sharing (CORS)([the guide](https://docs.pivotal.io/spring-cloud-gateway/1-1/managing-service-instances.html#cors))

```
cf bind-service cloud-native-spring my-gateway -c '{ "routes": [ { "path": "/menu/**" ,"filters": ["StripPrefix=0"] } ] , "cors": { "allowed-origins": [ "https://example.com" ]}'

```

*The End of this lab*

---
## Additional tasks

### accessing to a isolation segment 
([the guide](https://docs.pivotal.io/spring-cloud-gateway/1-1/managing-service-instances.html#isolation-segments))

```
cf bind-service cloud-native-spring my-gateway -c '{ "routes": [ { "path": "/menu/**" ,"filters": ["StripPrefix=0"] } ] ,"isolation-segments": [ "my-isolation-segment" ] }'

```
