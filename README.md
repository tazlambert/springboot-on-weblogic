# Welcome to Springboot On WebLogic Hands On Lab

This Hands On Lab will cover on creating simple Springboot project that will be deployed on top of WebLogic Server. For this purpose we will create simple hellow world page only. Knowledge on Java codes, Linux, Maven, and WebLogic are required.

## Create Springboot Project

To start springboot project the easiest way is by creating the project in [Spring Initialzr](https://start.spring.io/) all that need to be done is filling some criteria, for this lab we will fill it like below:

![](images/springboot1.png)

The above generate button will give you .zip project file that can be extracted and imported to your personal development tools. 

Then we need to create hello world program by modifying the codes in DemoApplication.java to become like below:
```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/hello")
@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

	@RequestMapping(method = RequestMethod.GET)
	String readResource() {
		return "Hello World!";
	}
}
```
After doing this you just need to build .jar file using maven, by going to the root project directory and do:
```
mvn clean install
mvn clean package
```
From the root project directory you will find **target** directory where you can find demo-0.0.1-SNAPSHOT.jar which you can run it directly using command
```
java -jar demo-0.0.1-SNAPSHOT.jar
```
Congratulations! You have just made a Springboot apps with embedded HTTP server, by default you can access it from http://localhost:8080/hello

## Modify Springboot Project for WebLogic

For this simple Hello World springboot project, no code changes to be made, for other complex springboot project it might be different though. To make this project can be deployed on WebLogic we need to create new folder under src/main directory
```
mkdir webapp
mkdir webapp/WEB-INF
```
Inside create new file named **weblogic.xml**
```
<?xml version = '1.0' encoding = 'UTF-8'?>
<weblogic-web-app
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://xmlns.oracle.com/weblogic/weblogic-web-app http://xmlns.oracle.com/weblogic/weblogic-web-app/1.9/weblogic-web-app.xsd"
        xmlns="http://xmlns.oracle.com/weblogic/weblogic-web-app">
    <context-root>/springboot</context-root>
    <container-descriptor>
        <prefer-application-packages>
            <package-name>org.slf4j.*</package-name>
            <package-name>org.springframework.*</package-name>
        </prefer-application-packages>
    </container-descriptor>

</weblogic-web-app>
```
The above file will tell the WebLogic server that this springboot projecet will use context root /springboot and some other package dependancy, then we need to create the second file in the same directory named **dispatcherServlet-servlet.xml**:
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```
Nothing to add to above file and the final changes that need to be done is on the **pom.xml** that need to be changed some of the lines into
```
	<groupId>com.example.springboot.wls</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>war</packaging>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>
  ```
Major changes is to change the packaging from **jar** to **war**. After this go to the root project directory and create .war file by doing
  ```
  mvn clean install
  mvn clean package
  ```
This will create .war file inside target directory which later you take that .war file then deploy it to your WebLogic server. If all went ok then you can access the hello world program from http://localhost:8001/springboot/hello and that's it the springboot application is converted into WebLogic hosted applications.
