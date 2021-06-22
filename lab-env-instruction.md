### Seat Number
```
|Instructor|
|  1 (179)  |  2 (180)  |   3 (181) |
|  4 (182)  |  5 (183)  |   6 (184) |
|  7 (185)  |  8 (186)  |   9 (187) |
|  10(188) |  11(189)  | 12((190) |
```
- jumpbox IP: 192.168.150.#Seat
- gitlab, jenkins, TAS apps manager  id/password: edu#Seat / ----

### VPN
https://www.fortinet.com/support/product-downloads

### Jumpbox access
- https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html
- putty: ssh ubuntu@jumpbox

### gitlab UI login
http://192.168.150.191:18080/

### jenkins UI login  
http://192.168.150.191:28080/


### Port forwarding
notepad.exe관리자원한으로 열고 C:\Windows\System32\drivers\etc\hosts에 아래 내용 추가.
```
127.0.0.2	apps.sys.data.kr
127.0.0.2	login.sys.data.kr
127.0.0.2	uaa.sys.data.kr
127.0.0.2  api.sys.data.kr
127.0.0.2  log-cache.sys.data.kr
127.0.0.2  cloud-native-spring.apps.data.kr
```
### cmd 창에서 127.0.0.2: 443를 나의 jumpbox:8443으로 portforwarding
```
netsh interface portproxy add v4tov4 listenport=443 listenaddress=127.0.0.2 connectport=8443 connectaddress=192.168.150.#seat
netsh interface portproxy show v4tov4
# netsh interface portproxy delete v4tov4 listenport=443 listenaddress=127.0.0.2
```

### TAS apps manager UI login
- https://apps.sys.data.kr
- edu#Seat / ----

### cf cli install: 
cf login -a api.sys.data.kr --skip-ssl-validation

#### JDK install
- https://github.com/ojdkbuild/ojdkbuild
- java -version

### install maven
- set JAVA_HOME=C:\Program Files\ojdkbuild\java-1.8.0-openjdk-1.8.0.292-1
- https://maven.apache.org/install.html 
- https://maven.apache.org/download.cgi
- set PATH = C:\Users\YGL\Downloads\apache-maven-3.8.1-bin\apache-maven-3.8.1\bin
- mvn 

### IDE
- https://www.jetbrains.com/ko-kr/idea/download/
- https://start.spring.io/
