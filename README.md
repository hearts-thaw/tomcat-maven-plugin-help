# Tomcat Maven plugin setup for IntelliJ IDEA Community Edition

## 0. Prerequisites 
+ [setup  JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
+ [setup  Maven](https://maven.apache.org/download.cgi) (preinstalled in IntelliJ IDEA)
+ [setup  Tomcat](https://tomcat.apache.org/download-90.cgi)  


## 1. Configs
+ **set** _JAVA_HOME_, _CATALINA_HOME_ (or _JAVA_BASE_, _CATALINA_BASE_; you might need configuring _JAVA_OPTS_, _CATALINA_OPTS_ later) and MAVEN_HOME
+ **in**: _%CATALINA_HOME%\conf\context.xml_  
  **add**:
```xml
<Context privileged="true">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
     	   allow="127\.0\.0\.1"/>
</Context>
```
+ **in**: %CATALINA_HOME%\conf\tomcat-users.xml  
  **add**:
```xml
<user username="YOUR_USERNAME" password="YOUR_PASSWORD" roles="standard,manager-gui, manager-script" />
```
+ **in**: *%userprofile%/.m2/settings.xml* (your Maven folder)  
  **add**:
```XML
<servers>
    <server>
        <id>YOUR_SERVER_NAME</id>
        <username>YOUR_USERNAME</username>
        <password>YOUR_PASSWORD</password>
    </server>
</servers>
```



## 2. In your project

+ **pom.xml**
  **add**:
```XML
...
<packaging>war</packaging>>
...
<dependencies>
	...
    <!-- https://mvnrepository.com/artifact/javax/javaee-api -->
    <dependency>
        <groupId>javax</groupId>
        <artifactId>javaee-api</artifactId>
        <version>8.0.1</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
...
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <url>http://localhost:8080/manager/text</url>
                <server>YOUR_SERVER_NAME</server> <!-- dont forget to change -->
                <path>/${project.build.finalName}</path>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.3</version>
        </plugin>
    </plugins>
</build>
```  
+ **Configure your servlets using web.xml**

## 3. How to run/deploy/redeploy/undeploy

+ *%CATALINA_HOME%\bin\startup.bat*  
to start up the server
+ *%CATALINA_HOME%\bin\shutdown.bat*  
to shut down the server

+ **in**: Run/Debug configurations ("Edit configurations" window next to Run/Debug buttons)
+ **add**: Add new configuration (Alt+Insert) -> Maven -> working directory: YOUR_PROJECT_NAME; command line: tomcat7:run (deploy/redeploy/undeploy - choose any); in "Before launch" window below: Run Maven Goal -> command line: clean
+ If you prefer to "run" (tomcat7:run) server from IDEA, then you don't need to start it up manually from startup.bat!


## 4. How to deploy your web application in ROOT (/)

+ In your pom.xml tomcat plugin &lt;configuration&gt;&lt;/configuration&gt; set &lt;path&gt;/&lt;/path&gt;
+ In _%CATALINA_HOME%\bin\conf\Catalina\localhost_ create **ROOT.XML** with this content: `<Context docBase="../deploy/YOUR_APPLICATION_NAME.war"/>`  

### (Dont forget to rename placeholders, for example: YOUR_USERNAME="tomcat"; YOUR_PASSWORD="tomcat"; YOUR_SERVER_NAME="tomcatserver")
