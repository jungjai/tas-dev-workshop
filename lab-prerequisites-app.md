please check following tools and connectivity.

## source code editor
An IDE (IntelliJ, Eclipse, VSCode)

## (optional) VPN to the lab environment.

## jumpbox(linux) connectivity
connect to jumpbox via command line tool or Putty.
```
$ ssh ubuntu@JUMPBOX_IP
```

check architecture (64 bit)
```
$ arch

x86_64
```

switch as root
```
sudo su
```

check installed cli. refer to [app prerequisites](lab-prerequisites-app.md)


## cli from the Jumpbox.
- JDK 8 or higher: Please ensure you have a JDK installed and not just a JRE, JAVA_HOME
```
$ sudo apt install openjdk-8-jdk

$ java -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-8u292-b10-0ubuntu1~18.04-b10)
OpenJDK 64-Bit Server VM (build 25.292-b10, mixed mode)
```
- maven v3+ or gradle v6+ installed: https://gradle.org[Gradle] 
```
$ sudo apt update
$ sudo apt install maven

```
## git
check if you can log in to gitlab dashboard. see if you can read or push from cli.
```
$ sudo apt-get install git

git version
git version 2.17.1

git clone <sample repo>

```

## check cf cli connection to TAS

- TAS 2.9.* support cf cli v6
```
$ wget -q -O - https://packages.cloudfoundry.org/debian/cli.cloudfoundry.org.key | sudo apt-key add -

$ echo "deb https://packages.cloudfoundry.org/debian stable main" | sudo tee /etc/apt/sources.list.d/cloudfoundry-cli.list

$ sudo apt-get update

$ sudo apt-get install cf-cli


$  cf version
cf version 6.53.0+8e2b70a4a.2020-10-01
```
- TAS 2.10+ support cf cli v7+

if not installed, install by referencing [offical doc](https://docs.cloudfoundry.org/cf-cli/getting-started.html#overview)

### 1. TAS api domain lookup on DNS
```
$ nslookup api.TAS-SYSTEM-DOMAIN-URL

$ dig api.TAS-SYSTEM-DOMAIN-URL
```

Let's take a look at the CF CLI options
```
$ cf version
$ cf help -a
$ cf help -a | grep login
```

check TAS api connection.

```
$ cf api https://api.TAS-SYSTEM-DOMAIN-URL --skip-ssl-validation
$ cf login
$ cf login -a API-URL -u USERNAME -p PASSWORD -o ORG -s SPACE

```

## spring  version
The Spring Cloud Services v3.1 tile is based on Spring Cloud `Hoxton`. [tile release note](https://docs.pivotal.io/spring-cloud-services/3-1/common/index.html). 
Spring Cloud Release `Hoxton` supports Spring Boot Release `2.2/2.3`. In this lab, we will use boot 2.3.* which is minimum suppored from spring initializr https://start.spring.io 

## Jenkins
check if you can log in to the jenkins dashboard.


## (optional) Syslog server for App Log draining test.
```
$ systemctl status rsyslog
```
check configuration
```
$ cat  /etc/rsyslog.conf

# provides UDP syslog reception
module(load="imudp")
input(type="imudp" port="514")

# provides TCP syslog reception
module(load="imtcp")
input(type="imtcp" port="514")

```

## (optinal) NFS service for test
- [lab setup nfs server for dev](lab-setup-nfsserver-for-dev.md)


# Additional materials
- [Cloud Foundry Cheat Sheet](http://www.appservgrid.com/refcards/refcards/dzonerefcards/rc207-010d-cloud-foundry.pdf)
