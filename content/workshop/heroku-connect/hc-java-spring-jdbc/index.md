+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Heroku Connect : Sync Heroku app with Salesforce using Java Spring and JDBC"

+++

1. [Introduction](#introduction)
2. [Clone the Source Code](#clone-the-source-code)
  * [Code](#code)
3. [About the Application](#about-the-application)
4. [Get Contacts Implementation](#get-contacts-implementation)
5. [Create Contact Form](#create-contact-form)
6. [Complete code Listing for the Controller](#complete-code-listing-for-the-controller)
7. [Compile the App Locally](#compile-the-app-locally)
  * [Procfile](#procfile)  
8. [Deploying to Heroku](#deploying-to-heroku)
  * [Add PostgreSQL Add-On](#add-postgresql-add-on)
  * [Add Heroku Connect Add-On](#add-heroku-connect-add-on)
  * [Configure Heroku Connect Add-On](configure-heroku-connect-add-on)
9. [Connect to PostgreSQL from Local Instance](#connect-to-postgresql-from-local-instance)
  * [Open the Remote App in Heroku](#open-the-remote-app-in-heroku)
  * [Show Contacts](#show-contacts)
  * [Create Contact](#create-contact)
10. [Summary](#summary)

## Introduction

This article shows how to run a [Spring Boot](http://projects.spring.io/spring-boot/) with PostgreSQL JDBC driver which connects to the database populated by Heroku Connect.

Figure 1 : Heroku-Connect Spring Boot Data flow

Figure 1 show the  how HerokuConnect Add-On interacts with Heroku PostgreSQL and Force.com behind the scenes.


Note : Make sure you have Java 1.8, Maven 3.3.3 installed and [Heroku Toolbelt](https://toolbelt.heroku.com/)


We will be using a Spring Boot Sample app available at [heroku connect spring boot](https://github.com/rajdeepd/heroku-connect-spring-boot-jdbc-rw)

This app has four rest endpoints 

* `@app.route('/')` shows the home page of the App
* `@app.route('/contacts')` which fetches list of contacts from Postgres table `salesforce`.
* `@app.route('/createcontactform')` shows the Create Contact Form
* `@app.route('/createcontact')` Called by the Create Contact Form to insert form data into PostgreSQL `salesforce.contact`

## Clone the Source Code 

Clone the source code using `git clone` and change into the cloned project directory `heroku-connect-spring-boot`

  ``` bash

    $ git clone https://github.com/rajdeepd/heroku-connect-spring-boot-jdbc-rw
    $ cd heroku-connect-spring-boot-jdbc-rw

  ```
### Code

This is a basic Spring-boot app which uses PostgrSQL JDBC driver to connect to the PostgreSQL database and return the list of contacts. It uses Thymeleaf to render the HTML template.

Structure of the App is Listed below

  ```
    |-- pom.xml
	|-- Procfile
	|-- README.md
	|-- src
	|   `-- main
	|       |-- java
	|       |   `-- com
	|       |       `-- example
	|       |           |-- Contact.java
	|       |           `-- HerokuConnectApplication.java
	|       |-- resources
	|       |   |-- application.properties
	|       |   `-- templates
	|       |       |-- contact.html
	|       |       |-- createcontact.html
	|       |       |-- home.html
	|       |       `-- result.html
	|       `-- webapp
	|           `-- assets
	|               |-- css
	|               |   |-- bootstrap.css
	|               |   `-- style.css
	|               `-- js
	|                   `-- bootstrap.js
  ```

## About the Application

Application is based on Spring MVC Architecture.

The Spring Web model-view-controller (MVC) framework uses  DispatcherServlet that dispatches requests to handlers. It has configurable handler mappings, view resolution, locale and theme resolution.
The default handler is based on the `@Controller` and `@RequestMapping` annotations, offering a wide range of handling methods.

Controllers provide access to the application behavior  defined through a service interface. Controllers interpret user input and transform it into a model that is represented to the user by the view (Thymeleaf in our case). Spring implements a controller in a abstract way, which enables developers to create a wide variety of controllers.

Since Spring 2.5  annotation-based programming model for MVC controllers that uses annotations `@RequestMapping`, `@RequestParam`, `@ModelAttribute` has been introduced. 

Controllers implemented using this style do not have to extend specific base classes or implement specific interfaces 
In our example we are using `@Controller` annotation to define the Controller and `@RequestMapping` to map rest endpoints to implementation methods.

Spring Boot developers always annotate the main class with `@Configuration`, `@EnableAutoConfiguration` and `@ComponentScan`. These annotations are frequently used together Spring Boot provides a convenient `@SpringBootApplication` alternative.

The `@SpringBootApplication` annotation is equivalent to using @Configuration, @EnableAutoConfiguration and @ComponentScan with their default attributes.  This is the annotation we are using in our controller listed below.

### View implementation with Thymeleaf Templates

[Thymeleaf](http://www.thymeleaf.org/)  is a  template engine capable of processing and generating HTML, XML etc. It can work both in web and non-web environments. It is better suited for serving the view layer of web applications.
It provides an optional integration with Spring MVC, so that it can be used  as a complete substitute of JSP.

We are using Thymeleaf templates for rendering the Contacts sent by the controller.

### Controller

Main Controller of the App is `HerokuConnectApplication` class which provides implementation of four rest Endpoints.

  ``` java
  @Controller
	@SpringBootApplication
	public class HerokuConnectApplication {

	}
  ```
Following four `@RequestMappings` have been implemented in the Controller `HerokuConnectApplication`

  ``` java
  @RequestMapping("/")
    public String home(Model model) {
        ...
    }

    @RequestMapping("/contacts")
    public String contacts(Model model) {
    	....
    }

    @RequestMapping(value="/createcontact", method= RequestMethod.POST)
    public String createContact(@ModelAttribute Contact contact, Model model) {
        ....
    }

    @RequestMapping("/createcontactform")
    public String createContactForm(Model model) {
        ....
    }
  ```
Before we look at the implementation of contacts() method, following code listing shows how the JDBC connection is obtained based on DATABASE_URL environment variable. This can be setup for a local PostgreSQL, or a PostgreSQL instance running in Heroku as shown in sections later in this tutorial.

In Heroku dynos running on Heroku this environment variable is setup autmatically

  ``` java
  private static Connection getConnection() throws URISyntaxException, SQLException {
		URI dbUri = new URI(System.getenv("DATABASE_URL"));

		String username = dbUri.getUserInfo().split(":")[0];
		String password = dbUri.getUserInfo().split(":")[1];
		String dbUrl = "jdbc:postgresql://" + dbUri.getHost() + ':'
                + dbUri.getPort() + dbUri.getPath()
                + "?sslmode=require";

		return DriverManager.getConnection(dbUrl, username, password);
	}
  ```

## Get Contacts Implementation

In the implementation of `contacts(Model model)` method following steps are followed:

1. Get the database `connection` using `getConnection()` method in the controller
2. Create a `Statement` object `stmt`
    ``` java
    Statement stmt = connection.createStatement();
    ```
3. Create the sql String to be executed which in our case is
    ``` java
    sql = "SELECT id, sfid,  firstname, lastname, name, email FROM salesforce.contact";
    ```
4. Execute the statement `stmt`
    ``` java
    ResultSet rs = stmt.executeQuery(sql);
    ```
5. Populate List of `com.example.Contact` Object for each contact and add to Model model. 
   Code listing for `Contact.` class
  [Contact](https://github.com/rajdeepd/heroku-connect-spring-boot/blob/master/src/main/java/com/example/Contact.java)

6. return the `contact.html` Thymeleaf template 
    ``` java
    model.addAttribute("contacts", contacts);
   	return "contact";
    ```
    ``` java
    @RequestMapping("/contacts")
	   public String contacts(Model model) {
	        try {
	            Connection connection = getConnection();
	            Statement stmt = connection.createStatement();
	            String sql;
	            sql = "SELECT id, sfid,  firstname, lastname, name, email" +
	            	" FROM salesforce.contact";
	            ResultSet rs = stmt.executeQuery(sql);
	            
	            List contacts = new ArrayList<>();
	            // Extract data from result set
	            while (rs.next()) {
	                int id = rs.getInt("id");
	                String sfid = rs.getString("sfid");
	                String name = rs.getString("name");
	                String first = rs.getString("firstname");
	                String last = rs.getString("lastname");
	                String email = rs.getString("email");
	                contacts.add(new Contact(id, sfid, first, last, email));
	            }
	            model.addAttribute("contacts", contacts);
	            return "contact";
	        } catch (Exception e) {
	            return e.toString();
	        }
	    }
    ```
  
  Thymeleaf template `contact.html` below uses variable set in Model to retrieve and display list of contacts.

    ``` html
    <table>
      <th><td>Id</td><td>SFID</td><td>First</td><td>Last</td><td>Email</td></th>
      <tr th:each="contact : ${contacts}">
        <td></td>
        <td th:text="${contact.id}" ></td>
        <td th:text="${contact.sfid}" ></td>
        <td th:text="${contact.first}" ></td>
        <td th:text="${contact.last}" ></td>
        <td th:text="${contact.email}" ></td>
        </tr>
		</table>
    ```

## Create Contact Form

This implementation returns the `createcontact.html` Thymeleaf template Listed below.

  ``` java
  @RequestMapping("/createcontactform")
    public String createContactForm(Model model) {
        model.addAttribute("contact", new Contact());
        return "createcontact";
    }
  ```
Create Contact Form html

  ``` html
  <!DOCTYPE HTML>
	<html xmlns:th="http://www.thymeleaf.org">
	<head>
	    <title>Heroku Connect Spring Boot Demo</title>
	    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
	    <link rel="stylesheet" href="../assets/css/bootstrap.css" 
	        th:href="@{/assets/css/bootstrap.css}" />
	    <link rel="stylesheet" href="../assets/css/thymeleaf-demo.css" 
	        th:href="@{/assets/css/style.css}" />
	</head>
	<body>
	<!--<div style="margin-top: 20px;margin-left: 50px">-->
	<div class="container">
	<h3> Contacts : </h3>
	    <form action="#" th:action="@{/createcontact}" th:object="${contact}" 
	        method="post">
	        <table border="0">
	            <tr>
	                <td colspan="2" align="center"><h4>Create Contact</h4></td>
	            </tr>
	            <tr>
	                <td>First Name:</td>
	                <td><input id="first" type="text" th:field="*{first}"/></td>
	            </tr>
	            <tr>
	                <td>Last Name:</td>
	                <td><input id="last" type="text" th:field="*{last}"/></td>
	            </tr>
	            <tr>
	                <td>Email:</td>
	                <td><input id="email" type="text" th:field="*{email}"/></td>
	            </tr>
	            <tr><td><input type="submit" name="Submit"/></td></tr>

	        </table>
	    </form>
	</div>
	</body>
	</html>
  ```
  
### Create Contact Rest Endpoint

Listing below inserts the Contact data in the Database using JDBC.

* Form parameters are passed from the Form into Contact object and extracted from there.
  
  ``` java
  model.addAttribute("contact", contact);
     int id = contact.getId();
     String first = contact.getFirst();
     String last = contact.getLast();
     String email = contact.getEmail();
  ```
* JDBC Connection object is fetched, JDBC statement is created and executed

  ``` 
  Connection connection = getConnection();
    Statement stmt = connection.createStatement();
    String sql;
    sql = "insert into salesforce.contact(firstname, lastname, email) values " +
           "('" + first  + "', '" + last + " ',' " + email +  "');";
 
    int result = stmt.executeUpdate(sql);
  ```
  ``` java 
  @RequestMapping(value="/createcontact", method= RequestMethod.POST)
    public String createContact(@ModelAttribute Contact contact, Model model) {
        model.addAttribute("contact", contact);
        int id = contact.getId();
        String first = contact.getFirst();
        String last = contact.getLast();
        String email = contact.getEmail();

        try {
            Connection connection = getConnection();
            Statement stmt = connection.createStatement();
            String sql;
            sql = "insert into salesforce.contact(firstname, lastname, email) values " +
                    "('" + first  + "', '" + last + " ',' " + email +  "');";
            System.out.println(sql);
            int result = stmt.executeUpdate(sql);
            System.out.println("execute update returned: " + result);
        }catch(Exception e){
            e.printStackTrace();
            model.addAttribute("Exception", e.toString());
        }
        return "result";
    }
  ```
Result html page returned just shows the inserted record 

## Complete code Listing for the Controller

Complete Code Listing can be found at [HerokuConnectApplication](https://github.com/rajdeepd/heroku-connect-spring-boot-jdbc-rw/blob/master/src/main/java/com/example/HerokuConnectApplication.java)

## Compile the App Locally

1. Execure the following commands to compile and build the jar file
  
  ``` bash
  $ mvn compile
    $ mvn package
  ```
2. Run the app using the following command locally

  ``` bash
  $ mvn spring-boot:run
  ```
Your app should now be running on [localhost:8080](https://localhost:8080)

### Procfile

There is already a Procfile which tells the Heroku what kind of Dyno is required and the source for the application.

  ```
  web: java -Dserver.port=$PORT -jar  target/heroku-connect-spring-boot-jdbc-rw-0.0.1-SNAPSHOT.jar
  ```
  
## Deploying to Heroku

  ``` bash
  $ heroku create
	$ git push heroku master
  ```
The App is deployed but still needs PostgreSQL to be configured along with Heroku Connect.

### Add PostgreSQL Add-On

Add Postgress Add-On as shown below. This step is optional and is required if heroku-postgresql addon is not already configured by heroku for your app.

  ``` bash
  $ heroku addons:create heroku-postgresql:hobby-dev
  ```

### Add Heroku Connect Add-On

Configure Heroku Connect Add-On. Command below configures Herok-Connect Add-On to the application.

  ``` bash
  $ heroku addons:create herokuconnect
  ```

### Configure Heroku Connect Add-On

1. Setup Connection

2. Enter Schema Name : This is the schema name underwhich database will be created.

3. Trigger OAuth 

4. Enter Salesforce.com developer account credentials

5. Create Mappings

6. Create Mappings Contacts : Choose the fields in Salesforce Schema which need to be mapped to Postgres Database in the application. Make sure you select the Writable checkbox

7. Explore Contacts in the Dashboard

## Connect to PostgreSQL from Local Instance

1. Find the DATABASE_URL of the remote database using the following command
  
  ``` bash
   $ heroku config
  ```
You will see the output as shown below, we have removed the actual values of `user_name`, `password` and `database_name`.

  ``` bash
  heroku config
	=== rd-spring-boot-hc-1 Config Vars
	DATABASE_URL:      postgres://<user_name>:<password>@ec2-107-20-136-89.compute-1.amazonaws.com:5432/<database_name>
	HEROKUCONNECT_URL: DATABASE_URL:salesforce
  ```
  
2. Set the DATABASE_URL environment variable

  ``` 
  export DATABASE_URL=postgres://<user_name>:<password>@ec2-107-20-136-89.compute-1.amazonaws.com:5432/<database_name>
  ```

3. Run the app locally again

  ```
  $ mvn spring-boot:run
  ```
  `
  **Note** :Make sure you kill previous instances of spring-boot instances started 
  `

The App opens up in the default route `/` with the following view

Browse to URL `http://localhost:8080/contacts` to see the list of contact names.

### Open the Remote App in Heroku

  ``` bash
  $ heroku open
  ```
The App opens up in the default route `/` with the following view

### Show Contacts

Browse to URL `http://{your-app-name}.herokuapp.com/contacts` to see the list of contact names.

### Create Contact

## Summary

In this workshop we learnt how to configure a **Pre-existing** Spring Boot Application to work with Heroku Connect. We used PostgreSQL JDBC driver for talking to the PostgreSQL database deployed on Heroku.
  