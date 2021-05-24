# Build a frontend application(cloud-native-app)
we will re-use the app from [previous lab: building-spring-boot-app-config](lab-developing-spring-boot-app-config.md). copy the cloud-native-spring app.

you can reference the [complete sample app on github](https://github.com/myminseok/spring-cloud-sample/tree/master/lab-spring-cloud-registry)

[spring cloud eureka reference doc](https://cloud.spring.io/spring-cloud-netflix/multi/multi__service_discovery_eureka_clients.html)


### Prerequisites
- [app prerequisites](lab-prerequisites-app.md)
- we will re-use the app from [previous lab: building-spring-boot-app-config](lab-developing-spring-boot-app-config.md)


## Develop the app

### 1. Enable Discovery Client on ConfigServerApplication
- add @EnableDiscoveryClient annotation to CloudNativeSpringApplication.java
```
package com.example.cloudnativespring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
@EnableDiscoveryClient
public class CloudNativeSpringApplication {

	public static void main(String[] args) {
		SpringApplication.run(CloudNativeSpringApplication.class, args);
	}

}
```

add GreeterController.java
```
...

@RestController
public class GreeterController {

    @Autowired
    private GreeterService greeter;

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @Bean
    @LoadBalanced
    public WebClient.Builder loadBalancedWebClientBuilder() {
        return WebClient.builder();
    }

    @RequestMapping(value = "/hello")
    public String hello(@RequestParam(value = "salutation", defaultValue = "Hello") String salutation,
                        @RequestParam(value = "name", defaultValue = "Bob") String name) {
        Greeting greeting = greeter.greetRestTemplate(salutation, name);
        return greeting.getMessage();
    }


    @RequestMapping("/hello-webclient")
    public Mono<String> helloWebclient(@RequestParam(value = "salutation", defaultValue = "Hello") String salutation,
                                       @RequestParam(value = "name", defaultValue = "Bob") String name) {

        return greeter.greetWebClient(salutation, name);
    }
}
```

SecurityConfiguration.java 

```

@Profile("dev")
@Configuration
@EnableWebFluxSecurity
@EnableReactiveMethodSecurity
public class SecurityConfiguration {

		@Bean
		SecurityWebFilterChain springWebFilterChain(ServerHttpSecurity http) throws Exception {
			return http
					// Demonstrate that method security works
					// Best practice to use both for defense in depth
					.authorizeExchange(exchanges -> exchanges
							.anyExchange().permitAll()
					)
					.httpBasic().disable()
					.build();
		}
}

```

add GreeterService.java

```

@Service
public class GreeterService {


	@Autowired
	private final RestTemplate rest;

	@Autowired
	private WebClient.Builder webClient;

	private static final String BACKEND_SERVICE="greeter-messages";

	private static final String URI_TEMPLATE = UriComponentsBuilder.fromUriString("http://"+BACKEND_SERVICE+"/greeting")
			.queryParam("salutation", "{salutation}")
			.queryParam("name", "{name}")
			.build()
			.toUriString();




	public GreeterService(RestTemplate restTemplate, WebClient.Builder webClient) {
		this.rest = restTemplate;
		this.webClient= webClient;
	}

	public Greeting greetRestTemplate(String salutation, String name) {
		Assert.notNull(salutation, "salutation is required");
		Assert.notNull(name, "name is required");

		return rest.getForObject(URI_TEMPLATE, Greeting.class, salutation, name);
	}

	public Mono<String> greetWebClient(String salutation, String name) {
		Assert.notNull(salutation, "salutation is required");
		Assert.notNull(name, "name is required");

		StringBuffer uri = new StringBuffer().append("/greeting?salutation=").append(salutation).append("&name=").append(name);

		return webClient.baseUrl("http://"+BACKEND_SERVICE)
				.build().get()
				.uri(uri.toString())
				.retrieve().bodyToMono(String.class);

	}

}

class Greeting {

	private final String message;

	@JsonCreator
	public Greeting(@JsonProperty("message") String message) {
		this.message = message;
	}

	public String getMessage() {
		return this.message;
	}

}
```


### 2. check app configuration
Set the spring.application.name property in application.yml.

```
spring:
  application:
    name: cloud-native-spring
  main:
    allow-bean-definition-overriding: true
  profiles.active: dev

server.port: 8080

eureka:
  client:
    serviceUrl:
      defaultZone: http://127.0.0.1:8761/eureka/

management:
  endpoints:
    web:
      exposure:
        include: '*'


cook.special: "Kim Chi in cloud-native-spring app"
...
```



### 3. Run the application
Now we're ready to run the application
```
  gradle bootRun

  mvn spring-boot:run
```

### 4. Testing the application on local PC
```
$ curl -k  http://localhost:8080/hello\?salutation=HI\&name=John

Hi, John!
```

## Deploy the app on TAS

### Create an application manifest.yml
[app manifest reference](lab-cf-manifest.md)
```
applications:
- name: cloud-native-spring
  random-route: true
  instances: 1
  path: ./build/libs/cloud-native-spring-1.0-SNAPSHOT.jar
  buildpacks: 
  - java_buildpack_offline
  stack: cflinuxfs3

  services:
  - my-config-server
  - 

  env:
    JAVA_OPTS: -Djava.security.egd=file:///dev/urandom
    TRUST_CERTS: api.cf.example.com
    SPRING_PROFILES_ACTIVE: dev
```

### Push application into Cloud Foundry
[lab-cf-push-app](lab-cf-push-app.md)
```
cf push -f manifest.yml
```

*The End of this lab*

---