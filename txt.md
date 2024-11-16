## Comprehensive Project Report

### Overview
The **Hello World Java Project** is structured to exemplify modern software development practices, incorporating Continuous Integration/Continuous Deployment (CI/CD) methodologies. This project utilizes a suite of tools including Java, Maven, Jenkins, SonarQube, Docker, and JaCoCo to create a scalable and maintainable application. The project emphasizes automation in the build and deployment processes while ensuring high code quality through testing and static analysis.

### Technologies and Tools Used
1. **Java**
   - **Version**: Java 17
   - **Purpose**: Core programming language for the application, providing modern features and performance enhancements.

2. **Maven**
   - **Role**: Build automation and dependency management tool.
   - **Functions**: Automates compilation, testing, packaging, and reporting. Configures plugins to streamline the project lifecycle.

3. **Jenkins**
   - **Functionality**: Automates CI/CD pipelines using a Jenkinsfile for version-controlled workflows.
   - **Integration**: Works seamlessly with tools like SonarQube and Docker.

4. **SonarQube**
   - **Purpose**: Performs static code analysis to identify bugs, vulnerabilities, and code smells.
   - **Coverage Tracking**: Integrates with JaCoCo for tracking unit test coverage.

5. **JaCoCo**
   - **Functionality**: Java Code Coverage tool that measures the extent of code tested during unit tests.
   - **Metrics Provided**:
     - Line Coverage: Percentage of lines executed during tests.
     - Branch Coverage: Percentage of decision branches executed.

6. **Docker**
   - **Usage**: Containerizes the application using a Dockerfile to provide consistent runtime environments.
   - **Purpose**: Simplifies deployment and enhances scalability.

7. **Git**
   - **Role**: Version control system for tracking code changes and facilitating collaboration among developers.

### Application Code
#### App.java
This file contains the main application logic.
- **Key Features**:
  - Entry point with the `main` method.
  - Method `getMessage()` returns "Hello World!" for testing purposes.

```java
package com.example;

public class App {
    public String getMessage() {
        return "Hello World!";
    }

    public static void main(String[] args) {
        System.out.println(new App().getMessage());
    }
}
```

#### AppTest.java
This file implements unit tests using JUnit to verify application logic.
- **Testing Method**:
  - Validates the output of `getMessage()`.

```java
package com.example;

import org.junit.Test;
import static org.junit.Assert.*;

public class AppTest {
    @Test
    public void testAppMessage() {
        App app = new App();
        assertEquals("Hello World!", app.getMessage());
    }
}
```

### Configuration Files
#### pom.xml
The Maven Project Object Model (POM) file defines project structure, dependencies, plugins, and properties.
- **Key Configurations**:
  - Dependencies include JUnit for testing.
  - Plugins configured for JaCoCo, Surefire, and Docker integration.

```xml
<plugins>
    <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>0.8.7</version>
        <executions>
            <execution>
                <goals>
                    <goal>prepare-agent</goal>
                    <goal>report</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
</plugins>
```

#### Dockerfile
Defines the containerization process for the application.
```dockerfile
# Use the official OpenJDK 17 image as the base image
FROM openjdk:17-jdk-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the application JAR file to the container
COPY target/hello-world-1.0-SNAPSHOT.jar app.jar

# Command to run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
```
- **Purpose**:
  - Ensures that the runtime environment matches development by using OpenJDK 17 as the base image.
  - Copies the JAR file built by Maven into the container.

#### Jenkinsfile
Defines the CI/CD pipeline for building, testing, analyzing, and deploying the application.
- **Pipeline Stages**:
  - Checkout: Retrieves source code from Git.
  - Build: Compiles and packages the application.
  - Test: Executes unit tests and publishes results.
  - SonarQube Analysis: Runs static code analysis.
  - Build Docker Image: Creates a Docker image using the Dockerfile.
  - Deploy: Deploys the application as a Docker container.

```groovy
pipeline {
    agent any 
    tools {
        maven 'Maven3.9.9'
        jdk 'JDK17'
    }
    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        DOCKER_CREDENTIALS = credentials('DOCKER_CREDENTIALS')
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build') {
            steps { sh 'mvn clean package' }
        }
        stage('Test') {
            steps { 
                sh 'mvn test' 
                post { always { junit '**/target/surefire-reports/*.xml' } }
            }
        }
        stage('SonarQube Analysis') {
            steps { 
                sh """ 
                mvn sonar:sonar \
                -Dsonar.projectKey=hello-world \
                -Dsonar.host.url=http://127.0.0.1:9000 \
                -Dsonar.token=$SONAR_TOKEN 
                """ 
            }
        }
        stage('Build Docker Image') {
            steps { sh 'docker build -t com.example/hello-world:1.0-SNAPSHOT .' }
        }
        stage('Deploy') {
            steps { 
                sh "docker run -d --name hello-world -p 80:8080 com.example/hello-world:1.0-SNAPSHOT" 
            }
        }
    }
}
```

### JaCoCo Integration
JaCoCo is integrated into the Maven build process to generate code coverage reports that provide insights into tested code segments:
- **Coverage Metrics Provided**:
  - Line Coverage Report: Indicates how many lines were executed during tests.
  - Branch Coverage Report: Indicates how many branches were executed during tests.

#### Usage:
To run tests with coverage:
```bash
mvn clean test jacoco:report
```
Coverage reports are saved in `target/site/jacoco/index.html`.

### Deployment Pipeline Overview
1. **Checkout Stage**: Retrieves source code from Git repository.
2. **Build Stage**: Executes `mvn clean package` to compile and package the application into a JAR file.
3. **Test Stage**: Runs unit tests via Surefire Maven Plugin; captures results for review.
4. **SonarQube Analysis Stage**: Uses SonarQube Scanner for Maven; uploads analysis results to SonarQube server.
5. **Docker Build Stage**: Builds Docker image using specified Dockerfile.
6. **Deploy Stage**: Runs new Docker image as a container, exposing it on port 80.

### Summary of Metrics
- **Code Coverage (JaCoCo)**:
  - Line Coverage indicates how much of your code was executed during tests.
  - Branch Coverage verifies decision-making parts of your code were tested adequately.

- **SonarQube Analysis Results**:
  Detects bugs, vulnerabilities, and provides insights into code quality standards adherence.

### Achievements
- Automated Build Process via Maven simplifies compilation and packaging tasks.
- Quality Assurance through JaCoCo and SonarQube ensures high standards in code quality and coverage metrics.
- Efficient Deployment through Docker standardizes runtime environments across different platforms.
- Scalability achieved through Jenkins CI/CD pipeline automating workflows for future enhancements.

This report encapsulates an in-depth analysis of the development lifecycle for this Java project, highlighting effective integration of industry-standard tools that contribute to its robustness, maintainability, and scalability in real-world applications.

1. Java:
Function: The core programming language used for developing the application.
- Provides the runtime environment for executing Java code.
- Offers a rich set of libraries and frameworks for various functionalities.

2. Maven:
Function: A build automation and project management tool.
- Manages project dependencies automatically.
- Standardizes the build process (compile, test, package, etc.).
- Provides a consistent project structure.
- Facilitates running tests and generating reports.

3. pom.xml:
Function: Project Object Model, the core of a project's configuration in Maven.
- Defines project structure and content.
- Declares project dependencies.
- Configures build plugins and their behavior.
- Sets project properties and profiles.

4. App.java:
Function: The main application class.
- Contains the core logic of the application.
- Provides an entry point (main method) for the program execution.

5. AppTest.java:
Function: The test class for the main application.
- Contains unit tests to verify the behavior of the App class.
- Helps ensure code quality and correctness.

6. Git:
Function: Version control system.
- Tracks changes in source code during software development.
- Facilitates collaboration among multiple developers.
- Maintains a history of code changes.

7. Jenkins:
Function: Continuous Integration/Continuous Deployment (CI/CD) tool.
- Automates the build process.
- Runs tests automatically when code changes are pushed.
- Integrates with other tools like SonarQube for code analysis.
- Provides a pipeline for software delivery.

8. Jenkinsfile:
Function: Defines the Jenkins Pipeline as code.
- Describes the entire build/test/deploy pipeline in a script.
- Allows version control of the CI/CD pipeline itself.
- Defines stages of the build process (checkout, build, test, analyze, build and push Docker image, deploy).

9. SonarQube:
Function: Code quality and security tool.
- Performs static code analysis to detect bugs, code smells, and security vulnerabilities.
- Tracks code coverage from unit tests.
- Provides a dashboard for code quality metrics.

10. Maven Compiler Plugin:
Function: Compiles Java source code.
- Allows specifying Java version for source code and bytecode.

11. Maven Surefire Plugin:
Function: Runs unit tests.
- Generates reports for test results.

12. JaCoCo Maven Plugin:
Function: Code coverage tool.
- Measures how much of your Java code is tested.
- Generates code coverage reports.

13. SonarQube Scanner for Maven:
Function: Analyzes code and uploads results to SonarQube server.
- Integrates SonarQube analysis into the Maven build process.

14. Maven JAR Plugin:
Function: Builds a JAR (Java ARchive) from the compiled project.
- Packages compiled classes and resources.
- Can configure the JAR to be executable.

15. Docker:
Function: Container platform for packaging and deploying applications.
- Provides a standardized way to build, package, and distribute applications.
- Ensures consistent runtime environment across different deployment environments.
- Enables easy scaling and management of application instances.

16. Jib Maven Plugin:
Function: Builds Docker images from Java applications.
- Automatically handles the Docker build process without a Dockerfile.
- Optimizes the image for faster build and deployment.
- Integrates with the Maven build lifecycle.

Each of these components plays a crucial role in the development, testing, and deployment process:

- Java and Maven form the core development environment.
- Git manages the source code versioning.
- Jenkins automates the build and deployment process.
- SonarQube ensures code quality.
- Docker and the Jib plugin handle the containerization of the application.
- The various Maven plugins handle specific tasks in the build lifecycle.

1. Set up Java:
   a. Download JDK 17 from Oracle's website or use OpenJDK.
   b. Install JDK 17 on your system.
   c. Set JAVA_HOME environment variable:
      - Windows: 
        1. Right-click on 'This PC' > Properties > Advanced system settings > Environment Variables
        2. Add new system variable: JAVA_HOME = C:\Program Files\Java\jdk-17 (adjust path as needed)
      - Linux/Mac: 
        Add to ~/.bashrc or ~/.bash_profile: export JAVA_HOME=/path/to/jdk-17
   d. Add Java to PATH:
      - Windows: Add %JAVA_HOME%\bin to Path in system variables
      - Linux/Mac: Add export PATH=$JAVA_HOME/bin:$PATH to ~/.bashrc or ~/.bash_profile
   e. Verify installation:
      Open a new terminal/command prompt and run: java -version

2. Set up Maven:
   a. Download Maven 3.9.9 from the Apache Maven website.
   b. Extract to a directory (e.g., C:\Program Files\Apache\maven)
   c. Set MAVEN_HOME environment variable:
      - Windows: Add new system variable MAVEN_HOME = C:\Program Files\Apache\maven
      - Linux/Mac: Add export MAVEN_HOME=/path/to/maven to ~/.bashrc or ~/.bash_profile
   d. Add Maven to PATH:
      - Windows: Add %MAVEN_HOME%\bin to Path in system variables
      - Linux/Mac: Add export PATH=$MAVEN_HOME/bin:$PATH to ~/.bashrc or ~/.bash_profile
   e. Verify installation:
      Open a new terminal/command prompt and run: mvn -version

3. Create Java Project with Maven:
   a. Open a terminal or command prompt
   b. Navigate to your desired project directory:
      cd /path/to/your/projects/directory
   c. Run Maven command to create a new project:
      mvn archetype:generate -DgroupId=com.example -DartifactId=hello-world -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

   Explanation of the command:
   - mvn: Calls the Maven command
   - archetype:generate: Tells Maven to generate a new project from a template (archetype)
   - -DgroupId=com.example: Sets the group ID (usually your organization's reversed domain name)
   - -DartifactId=hello-world: Sets the artifact ID (your project name)
   - -DarchetypeArtifactId=maven-archetype-quickstart: Specifies the template to use
   - -DinteractiveMode=false: Runs in non-interactive mode, using defaults

   d. Maven creates a new directory 'hello-world' with this structure:
      hello-world/
      ├── pom.xml
      └── src
          ├── main
          │   └── java
          │       └── com
          │           └── example
          │               └── App.java
          └── test
              └── java
                  └── com
                      └── example
                          └── AppTest.java

4. Update pom.xml:
   Navigate to the 'hello-world' directory and update pom.xml with the following content:

   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>com.example</groupId>
       <artifactId>hello-world</artifactId>
       <version>1.0-SNAPSHOT</version>
   
       <name>hello-world</name>
       <description>A simple Hello World project</description>
   
       <properties>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
           <maven.compiler.source>17</maven.compiler.source>
           <maven.compiler.target>17</maven.compiler.target>
           <junit.version>4.13.2</junit.version>
           <jacoco.version>0.8.7</jacoco.version>
           <sonar.java.coveragePlugin>jacoco</sonar.java.coveragePlugin>
           <sonar.dynamicAnalysis>reuseReports</sonar.dynamicAnalysis>
           <sonar.jacoco.reportPath>${project.basedir}/../target/jacoco.exec</sonar.jacoco.reportPath>
           <sonar.language>java</sonar.language>
           <docker.image.prefix>amritanshu310</docker.image.prefix>
           <docker.image.name>hello-world</docker.image.name>
           <docker.image.tag>${project.version}</docker.image.tag>
       </properties>
   
       <dependencies>
           <dependency>
               <groupId>junit</groupId>
               <artifactId>junit</artifactId>
               <version>${junit.version}</version>
               <scope>test</scope>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-compiler-plugin</artifactId>
                   <version>3.8.1</version>
                   <configuration>
                       <source>${maven.compiler.source}</source>
                       <target>${maven.compiler.target}</target>
                   </configuration>
               </plugin>
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-surefire-plugin</artifactId>
                   <version>3.0.0-M5</version>
               </plugin>
               <plugin>
                   <groupId>org.jacoco</groupId>
                   <artifactId>jacoco-maven-plugin</artifactId>
                   <version>${jacoco.version}</version>
                   <executions>
                       <execution>
                           <id>jacoco-initialize</id>
                           <goals>
                               <goal>prepare-agent</goal>
                           </goals>
                       </execution>
                       <execution>
                           <id>jacoco-site</id>
                           <phase>package</phase>
                           <goals>
                               <goal>report</goal>
                           </goals>
                       </execution>
                   </executions>
               </plugin>
               <plugin>
                   <groupId>org.sonarsource.scanner.maven</groupId>
                   <artifactId>sonar-maven-plugin</artifactId>
                   <version>3.9.1.2184</version>
               </plugin>
               <plugin>
                   <groupId>org.apache.maven.plugins</groupId>
                   <artifactId>maven-jar-plugin</artifactId>
                   <version>3.2.0</version>
                   <configuration>
                       <archive>
                           <manifest>
                               <addClasspath>true</addClasspath>
                               <mainClass>com.example.App</mainClass>
                           </manifest>
                       </archive>
                   </configuration>
               </plugin>
               <plugin>
                   <groupId>com.google.cloud.tools</groupId>
                   <artifactId>jib-maven-plugin</artifactId>
                   <version>3.3.1</version>
                   <configuration>
                       <from>
                           <image>openjdk:17</image>
                       </from>
                       <to>
                           <image>${docker.image.prefix}/${docker.image.name}:${docker.image.tag}</image>
                           <auth>
                               <username>${env.DOCKER_CREDENTIALS_USR}</username>
                               <password>${env.DOCKER_CREDENTIALS_PSW}</password>
                           </auth>
                       </to>
                   </configuration>
               </plugin>
           </plugins>
       </build>
   
       <profiles>
           <profile>
               <id>sonar</id>
               <activation>
                   <activeByDefault>true</activeByDefault>
               </activation>
               <properties>
                   <sonar.host.url>http://localhost:9000</sonar.host.url>
               </properties>
           </profile>
       </profiles>
   </project>

   Explanation of key elements:
   - <project>: Root element of the POM.
   - <modelVersion>: Specifies the POM model version (4.0.0 for Maven 2 & 3).
   - <groupId>, <artifactId>, <version>: The project's "coordinates".
   - <properties>: Defines project-specific properties, including Docker-related settings.
   - <dependencies>: Specifies project dependencies (e.g., JUnit for testing).
   - <build>: Configures the build process.
   - <plugins>: Specifies Maven plugins to use during the build, including the Jib plugin for Docker image creation.
   - <profiles>: Defines build profiles (here, for SonarQube).

5. Update Java files:
   a. Edit src/main/java/com/example/App.java:
      
      package com.example;
      
      public class App {
          public String getMessage() {
              return "Hello World!";
          }
      
          public static void main(String[] args) {
              System.out.println(new App().getMessage());
          }
      }

   Explanation:
   - package com.example;: Declares the package for this class.
   - public class App {: Defines a public class named App.
   - public String getMessage(): A method that returns "Hello World!".
   - public static void main(String[] args): The main method, entry point of the program.
   - System.out.println(new App().getMessage());: Creates an App instance and prints its message.

   b. Edit src/test/java/com/example/AppTest.java:
      
      package com.example;
      
      import org.junit.Test;
      import static org.junit.Assert.*;
      
      public class AppTest {
          @Test
          public void testAppMessage() {
              App app = new App();
              assertEquals("Hello World!", app.getMessage());
          }
      }

   Explanation:
   - import org.junit.Test;: Imports the JUnit Test annotation.
   - import static org.junit.Assert.*;: Imports JUnit assertion methods.
   - @Test: Marks this method as a test.
   - assertEquals("Hello World!", app.getMessage());: Asserts that app.getMessage() returns "Hello World!".

6. Set up Git:
   a. Install Git from git-scm.com
   b. Open terminal/command prompt in your project directory
   c. Initialize Git repository:
      git init
   d. Add files to Git:
      git add .
   e. Commit files:
      git commit -m "Initial commit"

7. Set up Jenkins:
   a. Download Jenkins from jenkins.io
   b. Install Jenkins
   c. Start Jenkins service
   d. Open browser and go to http://localhost:8080
   e. Follow setup wizard to install suggested plugins and create admin user

8. Configure Jenkins:
   a. Manage Jenkins > Global Tool Configuration
   b. Add JDK:
      - Name: JDK17
      - JAVA_HOME: Path to your JDK 17 installation
   c. Add Maven:
      - Name: Maven3.9.9
      - MAVEN_HOME: Path to your Maven installation
   d. Manage Jenkins > Manage Plugins
      - Install plugins: Git, Pipeline, JUnit, JaCoCo, SonarQube Scanner, Docker, Docker Pipeline

9. Set up SonarQube:
   a. Download SonarQube from sonarqube.org
   b. Extract and start SonarQube server
   c. Open browser and go to http://localhost:9000
   d. Log in with default credentials (admin/admin)
   e. Create a new project:
      - Manually:  Give it key 'hello-world' and display name 'HELLO WORLD'
   f. Generate a token for Jenkins to use
10. Set up Docker:
    a. Install Docker Desktop from the official Docker website.
    b. Create a Docker Hub account if you don't have one already.
    c. Log in to Docker from the command line:
       - Windows: `docker login`
       - Linux/Mac: `sudo docker login`

11. Configure SonarQube in Jenkins:
    a. Manage Jenkins > Configure System
    b. Find 'SonarQube servers' section
    c. Add SonarQube:
       - Name: SonarQube
       - Server URL: http://localhost:9000
    d. Manage Jenkins > Manage Credentials
    e. Add new credentials:
       - Kind: Username with password
       - Scope: Global
       - Username: Your Docker Hub username
       - Password: Your Docker Hub password
       - ID: DOCKER_CREDENTIALS

12. Create Jenkins Pipeline:
    a. Jenkins dashboard > New Item
    b. Enter name, select 'Pipeline', click OK
    c. In configuration:
       - Definition: Pipeline script from SCM
       - SCM: Git
       - Repository URL: Your Git repo URL
       - Script Path: Jenkinsfile
    d. Save

13. Create Jenkinsfile:
    In your project root, create a file named 'Jenkinsfile' with the following content:

    pipeline {
        agent any

        tools {
            maven 'Maven3.9.9'
            jdk 'JDK17'
        }

        environment {
            SONAR_TOKEN = credentials('SONAR_TOKEN')
            DOCKER_CREDENTIALS = credentials('DOCKER_CREDENTIALS')
        }

        stages {
            stage('Checkout') {
                steps {
                    checkout scm
                }
            }

            stage('Set Version') {
                steps {
                    script {
                        env.PROJECT_VERSION = bat(script: '@mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true).trim()
                        echo "Project version: ${env.PROJECT_VERSION}"
                    }
                }
            }

            stage('Build') {
                steps {
                    bat 'mvn clean package'
                }
            }

            stage('Test') {
                steps {
                    bat 'mvn test'
                }
                post {
                    always {
                        junit '**/target/surefire-reports/*.xml'
                    }
                }
            }

            stage('SonarQube Analysis') {
                steps {
                    bat """
                        mvn sonar:sonar ^
                        -Dsonar.projectKey=hello-world ^
                        -Dsonar.projectName="HELLO WORLD" ^
                        -Dsonar.host.url=http://127.0.0.1:9000 ^
                        -Dsonar.token=%SONAR_TOKEN%
                    """
                }
            }

            stage('Build and Push Docker Image') {
                steps {
                    bat 'mvn jib:build -Djib.to.auth.username=%DOCKER_CREDENTIALS_USR% -Djib.to.auth.password=%DOCKER_CREDENTIALS_PSW%'
                }
            }

            stage('Deploy') {
                steps {
                    bat "echo Project version: %PROJECT_VERSION%"
                    bat "docker pull amritanshu310/hello-world:%PROJECT_VERSION%"
                    bat "docker stop hello-world-prod || exit 0"
                    bat "docker rm hello-world-prod || exit 0"
                    bat "docker run -d --name hello-world-prod -p 80:8080 amritanshu310/hello-world:%PROJECT_VERSION%"
                }
            }
        }

        post {
            always {
                echo 'Pipeline finished'
            }
            success {
                echo 'Pipeline succeeded!'
                mail to: 'amritanshucodes@gmail.com',
                     subject: "Success: ${currentBuild.fullDisplayName}",
                     body: "The pipeline ${currentBuild.fullDisplayName} has completed successfully."
            }
            failure {
                echo 'Pipeline failed!'
                mail to: 'amritanshucodes@gmail.com',
                     subject: "Failed: ${currentBuild.fullDisplayName}",
                     body: "The pipeline ${currentBuild.fullDisplayName} has failed. Please check the console output for details."
            }
        }
    }

    Explanation of the new stages:

    1. **Set Version**: This stage extracts the project version from the Maven POM file and stores it in the `PROJECT_VERSION` environment variable.
    2. **Build and Push Docker Image**: This stage uses the Maven Jib plugin to build and push the Docker image to Docker Hub. The Jib plugin automatically handles the Docker build process without requiring a Dockerfile.
    3. **Deploy**: This stage pulls the latest Docker image from Docker Hub, stops and removes the existing container, and then runs a new container with the updated image, exposing the application on port 80.

    The pipeline will now automatically build, test, analyze, package as a Docker image, and deploy the Java application, making it easier to distribute and run the application in different environments.

14. Commit and push to your Git repository:
    git add Jenkinsfile
    git commit -m "Add Jenkinsfile"
    git push

15. Run Pipeline:
    a. Go to your Jenkins pipeline
    b. Click 'Build Now'
    c. Monitor the build progress in the console output

