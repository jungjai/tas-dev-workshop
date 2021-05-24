##  Setting up NFS service for test
connect to jumpbox via command line tool or Putty.
```
connect 
$ ssh ubuntu@JUMPBOX_IP
```

install nfs server
```
$ sudo apt install nfs-kernel-server



```

add nfs configuration. 
```

$ mkdir /home/ubuntu/nfs-test

$ chown -r ubuntu:ubuntu /home/ubuntu/nfs-test

$ sudo vi /etc/exports

# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/home/ubuntu/nfs-test *(rw,sync,no_root_squash,subtree_check)
```
> `/home/ubuntu/nfs-test`

restart nfs server

```
$ sudo systemctl status nfs-kernel-server

```
*The End of this lab*

---