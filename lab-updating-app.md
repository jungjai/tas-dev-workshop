# Rolling(no downtime) App Deployments

- [official doc](https://docs.cloudfoundry.org/devguide/deploy-apps/rolling-deploy.html)    


### prerequisites
- cf v7+
- cf v6.4+(experimental. donot use)


### pushing app with no downtime
```
cf push APP-NAME --strategy rolling
```
### restart app with no downtime
```
cf restart APP-NAME --strategy rolling
```

### stop app deployment
```
cf cancel-deployment APP-NAME
```

### View the Status of Rolling Deployments

Find the GUID of your app by running
```
cf app APP-NAME --guid
```
Find the deployment for that app by running
```
cf curl GET /v3/deployments?app_guids=APP-GUID&status_values=ACTIVE
```
> Where APP-GUID is the GUID of the app. Deployments are listed in chronological order, with the latest deployment displayed as the last in a list

Get deployment status
```
cf curl GET /v3/deployments/DEPLOYMENT-GUID
```
> status.value: Indicates if the deployment is ACTIVE or FINALIZED.

> status.reason: Provides detail about the deployment status.

> status.details: Provides the timestamp for the most recent successful health check. The value of the status.details property can be nil if there is no successful health check for the deployment. For example, there might be no successful health check if the deployment was cancelled

### App Revision
Rolling back to a previous revision: This allows you to deploy a version of the app that you had running previously without needing to track that previous state yourself or have multiple apps running. When you create a deployment and reference a revision, the revision deploys as the current version of your app.
- [official doc](https://docs.pivotal.io/application-service/2-10/devguide/revisions.html)

#### Roll Back to a Previous Revision
use Apps Manager UI.


*The End of this lab*

---

