
# Using an External Network File System (Volume Services)
guide: https://docs.pivotal.io/application-service/2-10/devguide/services/using-vol-services.html

### Prerequisites
- [setup nfs server for dev](lab-setup-nfsserver-for-dev.md)

## Create nfs service instance

```
cf marketplace
service   plans      description
nfs       Existing   Service for connecting to NFS volumes

cf create-service nfs Existing SERVICE-INSTANCE-NAME -c '{"share":"SERVER/SHARE", "version":"NFS-PROTOCOL"}'

```
> SERVICE-INSTANCE-NAME is a name you provide for this NFS volume service instance.

> SERVER/SHARE is the NFS address of your server and share.
Note: Ensure you omit the : that usually follows the server name in the address.

> (Optional) NFS-PROTOCOL is the NFS protocol you want to use. For example, if you want to use NFSv4, set the version to 4.1. Valid values are 3.0, 4.0, 4.1 or 4.2. If you do not specify a version, the protocol version used is negotiated between client and server at mount time. This usually results in the latest available version being used

#### for example

```
cf create-service nfs Existing nfs160 -c '{"share":"<NFS-SERVER-IP>/home/ubuntu/nfs-test"}'

```

## Push a sample app(pora)

```
git clone https://github.com/cloudfoundry/persi-acceptance-tests.git

cd ./persi-acceptance-tests/assets/pora
cf push pora --no-start
```


## Bind to the App

```
cf bind-service YOUR-APP SERVICE-NAME -c '{"uid":"UID","gid":"GID","mount":"OPTIONAL-MOUNT-PATH","readonly":true, "cache:true}'
```
> YOUR-APP is the name of the app for which you want to use the volume service.

> SERVICE-NAME is the name of the volume service instance you created in the previous step.

> (Optional) UID and GID are the UID and GID to use when mounting the share to the app. The GID and UID must be positive integer values. Provide the UID and GID as a JSON string in-line or in a file. If you omit uid and gid, the driver skips mapfs mounting and performs just the normal kernel mount of the NFS file system without the overhead associated with FUSE mounts

> (Optional) cache: When you run the cf bind-service command, Volume Services mounts the remote file system with attribute caching disabled by default. You can enable attribute caching using default values by adding "cache":true to the bind configuration JSON string


#### for example
go to NFS server

```
ubuntu@ubuntu18:~/nfs-test$ id
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lpadmin),126(sambashare)
```
bind service with app.
```
cf bind-service pora nfs160 -c '{"uid":"1000","gid":"1000","mount":"/opt/nfs"}'
```

## Start your app
```
$ cf start pora

$ cf env YOUR-APP
"VCAP_SERVICES": {
  "nfs": [
    {
      "credentials": {},
      "label": "nfs",
      "name": "nfs_service_instance",
      "plan": "Existing",
      "provider": null,
      "syslog_drain_url": null,
      "tags": [
        "nfs"
      ],
      "volume_mounts": [
        {
          "container_dir": "/var/vcap/data/153e3c4b-1151-4cf7-b311-948dd77fce64",
          "device_type": "shared",
          "mode": "rw"
        }
      ]
    }
  ]
}
```

## Test the Volume Service from Your App

The command writes a file to the share and then reads it back out again.
```
curl http://pora.YOUR-CF-DOMAIN.com

curl http://pora.YOUR-CF-DOMAIN.com/write

```
and check the files from the app and the nfs server
```

ubuntu@ubuntu18:~$ cf ssh cloud-native-spring

vcap@d76f0831-919f-45b4-7c5c-afef:~$ id
uid=1000(vcap) gid=1000(vcap) groups=1000(vcap)

vcap@d76f0831-919f-45b4-7c5c-afef:/opt/nfs$ ls 
drwxrwxr-x 2 1000 1000 4096 Jun  8 07:29 ./
drwxr-xr-x 1 root root   17 Jun  8 07:28 ../
```

*The End of this lab*

---