# Tanzu Application Service (TAS, Cloud Foundry) Developer Workshop

### Course introduction
This program is focused on enablimg customers through doing it rather than talking about it. Students will gain a deep understanding of Tanzu Application Service (TAS) and will learn the fundamentals of Spring, Spring Boot and Spring Cloud Service.

### Learning Outcomes
- Demonstrate the ability to build greenfield, cloud native applications using Java, and Spring Boot
- Using TAS for web application deployments
- Discuss the pros and cons of moving a monolith to a microservice-based architecture
- Explain how to implement common distributed systems patterns to mitigate costs/limitations on an existing microservices-based codebase
- deliverable: course lab guide, sample source code. 

### Ice Breaking


# Day 1
Understanding Cloud Foundry Basic features

### Lab Environment checking
- [lab prerequisites](lab-prerequisites-app.md)



### TAS(Tanzu Application Service) Overview
- [cloud native app concepts(12 factor)](https://12factor.net/ko/config)
- slides: cloud foundry concepts: PaaS platform
- [videos](https://www.youtube.com/watch?v=Z2oghhwoEO0)
- [labs: apps-manager](lab-apps-manager.md)

### Publishing java app
- slides `stanging & running app`: how the staging process works, describe the nature and lifecycle of buildpacks, and explain how they determine the runtime configuration of applications
- [lab: building spring boot app](lab-developing-spring-boot-app.md)

### Logging & metrics
- slides
- https://docs.cloudfoundry.org/loggregator/architecture.html
- [lab: logging-metrics](lab-logging-metrics.md)

### Resiliency
- slides
- [blog: 4 level HA](https://tanzu.vmware.com/content/blog/the-four-levels-of-ha-in-pivotal-cf)
- [labs: resiliency](lab-resiliency.md)


# Day 2
Using Backing Service and Upgrading app

### SSH into app container
- [lab: cf-ssh](lab-cf-ssh.md)

### Backing services
- slides
- [lab: backing services](lab-services.md)

### Draining logs
- [lab: drain-app-log](lab-drain-log.md)

### Upgrading 
- slides: ‘blue green’ deploy
- [lab: updating-app](lab-updating-app.md) using revision history
- [lab: blue-green-deployment](lab-blue-green.md)

# Day 3
Running cloud native apps
- slides: microservices

### spring cloud config

- [lab: building spring boot app with config](lab-developing-spring-boot-app-config.md)
- [lab: developing spring cloud config server locally ](lab-spring-cloud-config-local-server.md)
- [lab: developing spring cloud config client app](lab-spring-cloud-config-client-app.md)
- [lab: running app on TAS](lab-spring-cloud-config-on-TAS.md)

### spring cloud registry
- [lab: developing backend app](lab-spring-cloud-registry-local-backend-app.md)
- [lab: developing frontend app](lab-spring-cloud-registry-local-frontend-app.md)
- [lab: developing eureka-server locally](lab-spring-cloud-registry-local-server.md)
- [lab: running apps on TAS](lab-spring-cloud-registry-on-TAS.md)

### spring cloud gateway
- [lab: spring-cloud-gateway](lab-spring-cloud-gateway.md)

# Day 4 
Advanced topics including Persistent data and leveraging external CI/CD

### session state clustering
- [labs](lab-session-state-caching.md)

### NFS volume mounts
- [lab: using nfs sevice](lab-using-nfs-service.md)

### Using CI/CD
- [lab: CI/CD](lab-ci-cd.md)
