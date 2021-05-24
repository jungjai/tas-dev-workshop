## Design
Cloud Foundry contains [4 levels of recoverability](https://tanzu.vmware.com/content/blog/the-four-levels-of-ha-in-pivotal-cf). In this exercise, you will demonstrate the application recovery capability of the platform

## Prerequisites

#### Push app

- use sample app https://github.com/myminseok/articulate

- [lab: compiling java app and pushing](lab-publishing-app-java.md)

## Labs
### Scaling Horizontally
- Ensure you have two instances of your app running
- [official app scaling doc](https://docs.cloudfoundry.org/devguide/deploy-apps/cf-scale.html)
```
cf scale <app-name> -i 2
```

### Tail logs
Use cf to tail logs for your application in a terminal window
```
cf logs <app-name>
```

### Instance crashing
The application contains an endpoint that allows you to programmatically kill an instance. Another instance will spin up in seconds. 

### Killing an instance
In Apps Manager, kill one instances of the app. anothere instance will spin up in seconds.

*The End of this lab*

---
