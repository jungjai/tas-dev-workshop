
# Apps Manager
[official doc](https://docs.pivotal.io/application-service/2-10/operating/console-login.html)

## Prerequisites
- apps manager URL: https://apps.SYSTEM-DOMAIN
  > Open a browser and navigate to apps.SYSTEM-DOMAIN, where SYSTEM-DOMAIN is the system domain you retrieved in the previous step. For example, if the system domain is system.example.com, then point your browser to apps.system.example.com
- username/password

## Managing Orgs and Spaces Using Apps Manager
https://docs.pivotal.io/application-service/2-10/console/manage-spaces.html

## Managing User Roles with Apps Manager
https://docs.pivotal.io/application-service/2-10/console/console-roles.html

### create user in TAS
```
cf create-user
```

### add user to org/space
```
cf set-org-role
cf set-space-role
```

### Invite New Users
use Apps Manager

## Permission
https://docs.pivotal.io/application-service/2-10/console/dev-console.html#permissions



*The End of this lab*

---