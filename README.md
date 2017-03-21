# POC Using vue2 as frontend for spring boot
https://randyyaj.github.io/post/20161129_vue_spring/

1. Generate Spring Boot project https://start.spring.io/ e.g. vue2boot
2. Split project into separate modules
	1. Create directory structure
		```sh
		cd vue2boot
		mkdir backend frontend
		mv src backend
		```
	2. edit parent pom.xml
		```xml
		<?xml version="1.0" encoding="UTF-8"?>
		<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
			<modelVersion>4.0.0</modelVersion>

			<groupId>com.anecico.vue2boot</groupId>
			<artifactId>parent</artifactId>
			<version>0.0.1-SNAPSHOT</version>
			<packaging>pom</packaging>

			<name>parent</name>
			<description>Demo project for Spring Boot and angular 2</description>

			<parent>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-starter-parent</artifactId>
				<version>1.5.2.RELEASE</version>
				<relativePath/> <!-- lookup parent from repository -->
			</parent>

			<properties>
				<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
				<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
				<java.version>1.8</java.version>
			</properties>

			<modules>
		      <module>frontend</module>
		      <module>backend</module>
		  </modules>
		</project>
		```
	3. create backend pom.xml
		```xml
		<?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        	<modelVersion>4.0.0</modelVersion>
        
        	<artifactId>backend</artifactId>
        	<name>backend</name>
        
        	<parent>
        		<groupId>com.anecico.vue2boot</groupId>
        		<artifactId>parent</artifactId>
        		<version>0.0.1-SNAPSHOT</version>
        	</parent>
        
        	<dependencies>
        		<dependency>
        			<groupId>org.springframework.boot</groupId>
        			<artifactId>spring-boot-starter-actuator</artifactId>
        		</dependency>
        		<dependency>
        			<groupId>org.springframework.boot</groupId>
        			<artifactId>spring-boot-starter-web</artifactId>
        		</dependency>
        
        		<dependency>
        			<groupId>org.springframework.boot</groupId>
        			<artifactId>spring-boot-starter-test</artifactId>
        			<scope>test</scope>
        		</dependency>
        		<dependency>
        			<groupId>de.flapdoodle.embed</groupId>
        			<artifactId>de.flapdoodle.embed.mongo</artifactId>
        			<scope>test</scope>
        		</dependency>
        
        		<dependency>
                <groupId>com.anecico.vue2boot</groupId>
                <artifactId>frontend</artifactId>
                <version>${project.version}</version>
                <scope>runtime</scope>
            </dependency>
        	</dependencies>
        
        	<build>
        		<plugins>
        			<plugin>
        				<groupId>org.springframework.boot</groupId>
        				<artifactId>spring-boot-maven-plugin</artifactId>
        			</plugin>
        
        			<plugin>
        				<artifactId>maven-resources-plugin</artifactId>
        				<executions>
        					<execution>
        						<id>Copy App Content</id>
        						<phase>generate-resources</phase>
        						<goals>
        							<goal>copy-resources</goal>
        						</goals>
        						<configuration>
        							<outputDirectory>src/main/resources/public</outputDirectory>
        							<overwrite>true</overwrite>
        							<resources>
        								<resource>
        									<directory>${project.parent.basedir}/frontend/target/dist</directory>
        									<includes>
        										<include>static/</include>
        										<include>index.html</include>
        									</includes>
        								</resource>
        							</resources>
        						</configuration>
        					</execution>
        				</executions>
        			</plugin>
        		</plugins>
        	</build>
        </project>
		```
	4. create frontend pom
		```xml
		<?xml version="1.0" encoding="UTF-8"?>
        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        	<modelVersion>4.0.0</modelVersion>
        
        	<artifactId>frontend</artifactId>
        	<name>frontend</name>
        
        	<parent>
        		<groupId>com.anecico.vue2boot</groupId>
        		<artifactId>parent</artifactId>
        		<version>0.0.1-SNAPSHOT</version>
        	</parent>
        
        
        	<build>
        		<plugins>
        			<plugin>
                  <groupId>com.github.eirslett</groupId>
                  <artifactId>frontend-maven-plugin</artifactId>
                  <version>1.3</version>
        
                  <configuration>
                    <nodeVersion>v6.9.1</nodeVersion>o
                    <npmVersion>4.0.3</npmVersion>
                    <workingDirectory>src/main/vue2boot</workingDirectory>
                  </configuration>
        
                  <executions>
                    <!-- Install our node and npm version to run npm/node scripts-->
                    <execution>
                      <id>install node and npm</id>
                      <goals>
                          <goal>install-node-and-npm</goal>
                      </goals>
                      <configuration>
                        <nodeVersion>v6.9.1</nodeVersion>
                        <npmVersion>3.10.9</npmVersion>
                        <nodeDownloadRoot>https://nodejs.org/dist/</nodeDownloadRoot>
                        <npmDownloadRoot>http://registry.npmjs.org/npm/-/</npmDownloadRoot>
                      </configuration>
                    </execution>
        
                    <!-- Set NPM Registry -->
                    <execution>
                      <id>npm set registry</id>
                      <goals>
                        <goal>npm</goal>
                      </goals>
                      <configuration>
                        <arguments>config set registry https://registry.npmjs.org</arguments>
                      </configuration>
                    </execution>
        
                    <!-- Set SSL privilege -->
                    <execution>
                      <id>npm set non-strict ssl</id>
                      <goals>
                        <goal>npm</goal>
                      </goals>
                      <!-- Optional configuration which provides for running any npm command -->
                      <configuration>
                        <arguments>config set strict-ssl false</arguments>
                      </configuration>
                    </execution>
        
                    <!-- Install all project dependencies -->
                    <execution>
                      <id>npm install</id>
                      <goals>
                        <goal>npm</goal>
                      </goals>
                      <!-- optional: default phase is "generate-resources" -->
                      <phase>generate-resources</phase>
                      <!-- Optional configuration which provides for running any npm command -->
                      <configuration>
                        <arguments>install</arguments>
                      </configuration>
                    </execution>
        
                    <!-- Build and minify static files -->
                    <execution>
                      <id>npm run build</id>
                      <goals>
                        <goal>npm</goal>
                      </goals>
                      <configuration>
                        <arguments>run build</arguments>
                      </configuration>
                    </execution>
                  </executions>
              </plugin>
        		</plugins>
        	</build>
        </project>
		```
3. Install vue2 app
    If vue-cli is not yet installed, install it with
    ```sh
    npm install -g vue-cli
    ```
    1. create a new project using the "webpack" template and install deps
        ```sh
        vue init webpack frontend
        cd frontend
        npm install
        ```
4. Tell webpack to output the dist/ contents to target/

    locate the app/frontend/config/index.js file and replace the following lines:
    ```javascript
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist'),
    ```
    with
    ```javascript
    index: path.resolve(__dirname, '../target/dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../target/dist'),
    ```
    
    


