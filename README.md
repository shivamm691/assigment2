# Web Application Deployment

This project involves the setup and deployment of a web application consisting of a frontend and a backend service running on the same server. The backend application is a Java 17 Spring application built using Gradle, while the frontend can be developed using any modern framework (e.g., React, Angular, Vue).

## Table of Contents

- [Environment Setup](#environment-setup)
- [Backend Application Setup](#backend-application-setup)
- [Frontend Application Setup](#frontend-application-setup)
- [Deployment Strategy](#deployment-strategy)
- [Continuous Integration/Continuous Deployment (CI/CD)](#continuous-integrationcontinuous-deployment-cicd)
- [Monitoring and Logging](#monitoring-and-logging)

## Environment Setup

1. **Server Preparation**:
    - Choose an operating system (Ubuntu, CentOS, etc.) and install it on your server.

2. **Install Java 17**:
    ```bash
    sudo apt update
    sudo apt install openjdk-17-jdk
    ```

3. **Install Gradle**:
    ```bash
    sudo apt install gradle
    ```

4. **Install Web Server**:
    - For Nginx:
      ```bash
      sudo apt install nginx
      ```

## Backend Application Setup

1. **Develop/Use Existing Spring Application**:
    - Ensure you have a `build.gradle` file like this:
    ```groovy
    plugins {
        id 'org.springframework.boot' version '2.5.4'
        id 'io.spring.dependency-management' version '1.0.11.RELEASE'
        id 'java'
    }

    group = 'com.example'
    version = '0.0.1-SNAPSHOT'
    sourceCompatibility = '17'

    repositories {
        mavenCentral()
    }

    dependencies {
        implementation 'org.springframework.boot:spring-boot-starter-web'
        testImplementation 'org.springframework.boot:spring-boot-starter-test'
    }

    test {
        useJUnitPlatform()
    }
    ```

2. **Build the Spring Application**:
    ```bash
    gradle build
    ```

## Frontend Application Setup

1. **Develop Frontend**: Use a framework like React.
2. **Build Frontend**:
    ```bash
    npm run build
    ```
    This will generate static files in the `build` directory (for React).

## Deployment Strategy

1. **Deploy Backend**:
    - Copy the built `.jar` file to your server.
    - Run the Spring application:
      ```bash
      java -jar your-application.jar
      ```

2. **Deploy Frontend**:
    - Copy the static files to the web server document root (e.g., `/var/www/html` for Nginx).

3. **Configure Web Server**:
    - For Nginx, update the configuration file (e.g., `/etc/nginx/sites-available/default`):
    ```nginx
    server {
        listen 80;

        server_name your_domain.com;

        location / {
            root /var/www/html;
            index index.html index.htm;
        }

        location /api/ {
            proxy_pass http://localhost:8080/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
    ```

## Continuous Integration/Continuous Deployment (CI/CD)

1. **Set Up CI/CD**: Example using GitHub Actions (`.github/workflows/ci.yml`):
    ```yaml
    name: CI

    on: [push]

    jobs:
      build:

        runs-on: ubuntu-latest

        steps:
        - uses: actions/checkout@v2
        - name: Set up JDK 17
          uses: actions/setup-java@v2
          with:
            java-version: '17'
        - name: Build with Gradle
          run: ./gradlew build

      deploy:

        needs: build

        runs-on: ubuntu-latest

        steps:
        - name: Deploy to Server
          run: |
            scp -r build/ user@your_server:/var/www/html
            ssh user@your_server 'sudo systemctl restart nginx'
    ```

## Monitoring and Logging

1. **Set Up Monitoring**:
    - Install Prometheus and Grafana.
    - Configure them to monitor your Spring application.

2. **Set Up Logging**:
    - Configure your Spring application to log to a file.
    - Use ELK Stack to aggregate and visualize logs.

## Conclusion

By following these steps, you can successfully set up and deploy your web application with a backend Spring application and a frontend on the same server. Ensure you have proper monitoring and logging in place to maintain the health of your application.
