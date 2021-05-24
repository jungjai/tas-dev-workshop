https://docs.cloudfoundry.org/devguide/ssh-index.html

## Accessing Apps with SSH
https://docs.cloudfoundry.org/devguide/deploy-apps/ssh-apps.html

## Check SSH Permissions

```
$ cf ssh-enabled MY-AWESOME-APP
ssh support is disabled for 'MY-AWESOME-APP'

$ cf space-ssh-allowed SPACE-NAME
ssh support is enabled in space 'SPACE-NAME'
```

## ssh into an App Container with cf SSH
```
$ cf ssh MY-AWESOME-APP
$ cf ssh MY-AWESOME-APP -i 2
```
> The -i flag targets a specific instance of an app. To log into the VM container hosting the third instance, index=2, of MY-AWESOME-APP, run. default i=0

## ssh turnneling 
https://docs.cloudfoundry.org/devguide/deploy-apps/ssh-services.html
```
$ cf ssh -L <localport>:<remotedb>:3306 YOUR-HOST-APP
$ mysql -u <username> -h 0 -p -D <database> -P <localport>
```

## Transfer files to/from the container

#### Run cf app MY-AWESOME-APP --guid and record the GUID of your target app.

```
$ cf app MY-AWESOME-APP --guid
abcdefab-1234-5678-abcd-1234abcd1234
```

#### get one-time authorization code that substitutes for an SSH password
```
$ cf ssh-code
E1x89n
```

#### scp files
you can use scp to download files 
```
scp -P 2222 -o User=cf:<APP-GUID>/<CONTAINER-INDEX> ssh.MY-DOMAIN.com:REMOTE-FILE-TO-RETRIEVE LOCAL-FILE-DESTINATION

scp -P 2222 -o User=cf:abcdefab-1234-5678-abcd-1234abcd1234/0 ssh.MY-DOMAIN.com:REMOTE-FILE-TO-RETRIEVE LOCAL-FILE-DESTINATION
```
>  For the username, use a string of the form cf:APP-GUID/APP-INSTANCE-INDEX@SSH-ENDPOINT, where APP-GUID and SSH-ENDPOINT come from the previous steps. 

> For the port number, use 2222

> APP-INSTANCE-INDEX is the index of the instance you want to access.


upload files to the container
```
scp -P 2222 -o User=cf:abcdefab-1234-5678-abcd-1234abcd1234/0 LOCAL-FILE-TO-COPY ssh.MY-DOMAIN.com:REMOTE-FILE-DESTINATION

```

*The End of this lab*

---
