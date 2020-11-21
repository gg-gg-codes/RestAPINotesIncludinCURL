1. eureka server on local

@springbootapplication
@EnableEurekaServer
public class App {
  psvm() {
    springapplication.run(App.class, args);
  }
}

* include maven dependecy

application.properties
server.port=8010
spring.application.name=discoveryservice
eureka.client.registerWithEureka=false
eureka.client.fetchRegistry=false
eureka.client.serviceUrl.defaultZone=http://localhost:8010/eureka

on browser go and see localhost:8010

u r done now.

2. 
