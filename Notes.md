1. eureka server on local

@springbootapplication
@EnableEurekaServer
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
}

* include maven dependecy
 1. eureka discovery and eureka server aka-> spring cloud starter netflix eureka client , spring cloud starter netflix eureka server

application.properties
server.port=8010
spring.application.name=discoveryservice
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=false
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka

on browser go and see localhost:8010

u r done now.

2. below users service to be registered on eureka

dependecy -> eureka discovery aka-> spring cloud starter netflix eureka client


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

controller

@restcontorller
@reaquestmapping("/users")
public class Userscontrollere {
    
    @GetMapping("/status/check")
    public sting status(){
    }

}

when u run this service , it will be running on different port than 0

3. create exactly similar account service, when u will run , it will get assigned different port

4. zuul api gateway :
dependency-> eureka discovery, zuul, dev tools, web
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

now zuul is client of eureka and zuul is api gateway and now you can acccess users and account
service thru zuul api gateway

5. Zuul load balancer - how zuul api gateway will work as load balancer

for user ms and account ms , you need to make some changes in properties file for to be register the service with different instance id

application.properties
server.port=${PORT:0} #for making sure that user ms will get random port while running and registrteing on eureka server
spring.application.name=users-ws
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka
spring.devtools.restart.enabled=true
eureka.instance.instance-id=${spring.application.name}:${spring.application.instance_id:${random.value}}

now with above configurations , same ms will get registered with different isntance id in eureka server.


