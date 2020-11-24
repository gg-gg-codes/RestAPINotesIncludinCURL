1. eureka server on local
```
@springbootapplication
@EnableEurekaServer
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
}
```
* include maven dependecy
 1. eureka discovery and eureka server aka-> spring cloud starter netflix eureka client , spring cloud starter netflix eureka server
```
application.properties
server.port=8010
spring.application.name=discoveryservice
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=false
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
```
on browser go and see localhost:8010

u r done now.

2. below users service to be registered on eureka

dependecy -> eureka discovery aka-> spring cloud starter netflix eureka client

```
@springbootapplication
@EnableDiscoveryClient
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
}

application.properties
server.port=0 #for making sure that user ms will get random port while running and registrteing on eureka server
spring.application.name=users-ws
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
spring.devtools.restart.enabled=true
```
```
controller

@restcontorller
@reaquestmapping("/users")
public class Userscontrollere {
    
    @GetMapping("/status/check")
    public sting status(){
    }

}
```
when u run this service , it will be running on different port than 0

3. create exactly similar account service, when u will run , it will get assigned different port

4. zuul api gateway :
dependency-> eureka discovery, zuul, dev tools, web
```
@springbootapplication
@EnableEurekaClient
@EnableZuulProxy
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
}

application.properties
server.port=8011
spring.application.name=zuul
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
```
now zuul is client of eureka and zuul is api gateway and now you can acccess users and account
service thru zuul api gateway

5. Zuul load balancer - how zuul api gateway will work as load balancer

for user ms and account ms , you need to make some changes in properties file for to be register the service with different instance id
```
application.properties
server.port=${PORT:0} #for making sure that user ms will get random port while running and registrteing on eureka server
spring.application.name=users-ws
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
spring.devtools.restart.enabled=true
eureka.instance.instance-id=${spring.application.name}:${spring.application.instance_id:${random.value}}
```
now with above configurations , same ms will get registered with different isntance id in eureka server.

```
mvn spring-boot:run -Dspring-boot.run.arguments=--spring.application.instance_id=sergey    -> 
by this ms will start with supplied argument name 'sergey'
```
```
mvn spring-boot:run -Dspring-boot.run.arguments=--spring.application.instance_id=sergey2,--server.port=8999 ->
by this ms wills tart with supplied argument name 'sergey2' and with port 8999
```

6.  To see the currently used port by MS, use Environment object
```
@restcontorller
@reaquestmapping("/users")
public class Userscontrollere {
    
    @Autowired
    private Environment env;
    
    @GetMapping("/status/check")
    public sting status(){
      return "working on port " + env.getProperty("local.server.port");
    }

}
```


7. security in springboot

dependency:
spring-boot-starter-security

```
@Configuration
@EnableWebSecurity
public class WebSecurity extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable();
    http.authorizedRequests().antMatches("users/**").permitAll();
    http.headers().frameOptions().disable();
    
  }
}
```
with csrf , we prevent the attack on web app.
with /users/** its going to allow request matching these urls 


8. password encoder
BCryptPasswordEncoder 
```
@springbootapplication
@EnableDiscoveryClient
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
  
  @Bean
  public BCryptPasswordEncoder bCryptPasswordEncoder() {
   return new BCryptPasswordEncoder();
  }
}

@Service
public class UsersServiceImpl implements UsersService {
      UsersRepository usersRepository;
      BCryptPasswordEncoder bCryptPasswordEncoder;
      
      @Autowired
      public UsersServiceImpl(UsersRepository usersRepository, BCryptPasswordEncoder bCryptPasswordEncoder) {
          this.usersRepository=usersRepository;
          this.bCryptPasswordEncoder=bCryptPasswordEncoder;
      }
      
      use like this -> bCryptPasswordEncoder.encode(requestBean.getPassword()) -> gonna encode the password
}
```

9. allow IP address of only zuul api gateway

```
application.properties
server.port=0 #for making sure that user ms will get random port while running and registrteing on eureka server
spring.application.name=users-ws
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
spring.devtools.restart.enabled=true
gateway-ip=192.168.2.81
```
```
@Configuration
@EnableWebSecurity
public class WebSecurity extends WebSecurityConfigurerAdapter {

  private Environment environment;
  
  @Autowired
  public WebSecurity(Environment environment) {
    this.environment=environment;
  }
  
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable();
    http.authorizedRequests().antMatchers("/**").hasIpAddress(environment.getProperty("gateway-ip"));
    http.headers().frameOptions().disable();
    
  }
}
```

10. loginrequestmodel
```
public class LoginRequestModel {

string email
string passsword;
//getter setter

}
```
