---
title: How to Configure Activiti 6.0 to Connect MySQL 8.0
date: 2021-05-25 17:41:13
tags: Activiti
categories:
- Java
- Java Web
---



## Download

Downloading the Activiti UI WAR file from the [Activiti website](http://www.activiti.org/).

Unzip `activiti-6.0.0.zip`.

Copy the `activiti-6.0.0/wars/activiti-app.war` to the `webapps` directory of Tomcat.

## Configuration

Updating configurations in `activiti-app/WEB-INF/classes/META-INF/activiti-app/activiti-app.properties`

```properties
datasource.driver=com.mysql.cj.jdbc.Driver
datasource.url=jdbc:mysql://127.0.0.1:3306/activiti6ui?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=CONVERT_TO_NULL&serverTimezone=GMT
datasource.username={your_username}
datasource.password={your_password}

hibernate.dialect=org.hibernate.dialect.MySQLDialect
```

Adding MySQL 8 driver [mysql-connector-java-8.0.19.jar](https://mvnrepository.com/artifact/mysql/mysql-connector-java) to lib directory `activiti-app/WEB-INF/lib`.

## Initializing Database

Creating database `activiti6ui` that configuring in `activiti-app/WEB-INF/classes/META-INF/activiti-app/activiti-app.properties`.

Executing following Activiti initial SQL files:

- `activiti-6.0.0/database/create/activiti.mysql.create.engine.sql`
- `activiti-6.0.0/database/create/activiti.mysql.create.history.sql`
- `activiti-6.0.0/database/create/activiti.mysql.create.identity.sql`

## Running and Visiting

Start Tomcat by running the startup.bat or startup.sh scripts in the bin folder of Tomcat.

When Tomcat is started open your browser and go to http://localhost:8080/activiti-app. Login with admin and password test.
