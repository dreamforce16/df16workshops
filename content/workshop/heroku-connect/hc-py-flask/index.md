+++
date = "2016-07-29T16:18:23+05:30"
draft = true

title = "Heroku Connect : Sync Heroku app with Salesforce using Python Flask"

+++

1. [Introduction](#introduction)
2. [Install Virtual Environment](#install-virtual-environment)
3. [Creating a Simple Flask App](#creating-a-simple-flask-app)
4. [Initialize Git](#initialize-git)
5. [Create a Requirements File](#create-a-requirements-file)
6. [Create a Procfile](#create-a-procfile)
7. [Deploying to Heroku](#deploying-to-heroku)
8. [Add PostgreSQL Add-On](#add-postgresql-add-on)
9. [Add Heroku Connect Add-On](#add-heroku-connect-add-on)
10. [Configure Heroku Connect Add-On](#configure-heroku-connect-add-on)
11. [Add Code for contacts endpoint](#add-code-for-contacts-endpoint) 
12. [Add Jinja Template](#add-jinja-template)
13. [Update python packages](#update-python-packages)
14. [Add Requirements file](#add-requirements-file)
15. [Update Changes in Heroku](#update-changes-in-heroku)
16. [Show Contacts](#show-contacts)
17. [Show Contacts Locally](#show-contacts-locally)
18. [Executing using PyCharm](#executing-using-pycharm)
17. [Summary](#summary)
  
# Introduction

This workshop shows how to **Create** and **Run** a Python app with psycopg2 which uses PostgreSQL based Heroku Connect

<img src="images/heroku-connect-flow-flask-psycopg2.png" width="70%" height="70%">

Figure 1 show how the HerokuConnect Add-On interacts with Heroku Postgres and force.com behind the scenes.
Make sure you have [Python](https://www.python.org/downloads/) installed.  Also, install the [Heroku Toolbelt](https://toolbelt.heroku.com/)

We will be using a basic Flask app available at flask_psycopg2_v2

This app has four rest endpoints

    @app.route('/') which displays Hello world
    @app.route('/contacts') which fetches list of contacts from Postgres table salesforce.contact
    @app.route('/create_contact', methods=['POST','GET']) which creates a new contact
    @app.route('/contactform') which serves an HTML Form for entering new contact details

## Clone the Source Code 

``` bash

$ git clone https://github.com/rajdeepd/flask-psycopg2-v2

```

## Install Virtual Environment

Locally, create a folder `flask-psycopg2-sample` and install a virtual environment in it.

``` bash

$ cd flask-psycopg2-v2
$ virtualenv venv
$ source venv/bin/activate

```
Install Dependencies

``` bash

$ pip install flask gunicorn psycopg2

```


Run the app using the following command

    ``` bash
    $ python app.py
    ```

Your app should now be running on [localhost:5000](http://localhost:5000)

## Requirements File

*Note : This section is for information only*

Notice that the Requirements file already exists, this will be used by Heroku to setup the dynos

```bash

    $ cat requirements.txt
    Flask==0.10.1
    gunicorn==19.3.0
    itsdangerous==0.24
    Jinja2==2.8
    MarkupSafe==0.23
    psycopg2==2.6.1
    Werkzeug==0.11
    wheel==0.24.0

```

## Procfile

*Note : This section is for information only*

There is already a Procfile which tells the Heroku what kind of Dyno is required and the source for the application.

``` bash

    web: gunicorn app:app --log-file -

```

## DB Initialization

*Note : This section is for information only*

We will parsing the DATABASE_URL environment variable to connect to PostgreSQL database. The PostgreSQL Python driver is already configured in the requirements file.

Steps are

1. Parse DATABASE_URL into variable `url`
2. Create a connection string `db` from the parsed `url`.
3. Create a Database connection `conn`.
4. Open a Database cursor `cur`.

``` python

url = urlparse.urlparse(os.environ.get('DATABASE_URL'))
db = "dbname=%s user=%s password=%s host=%s " % (url.path[1:], 
     url.username, url.password, url.hostname)
schema = "schema.sql"
conn = psycopg2.connect(db)
cur = conn.cursor()

```
## Flask Controller

*Note : This section is for information only*

`app.py` is the main controller for our applications and code listing below shows the implementation of various use cases

### List Contacts

``` python 
@app.route('/contacts')
def contacts():
    try:
        cur.execute("""SELECT name from salesforce.contact""")
        rows = cur.fetchall()
        response = ''
        my_list = []
        for row in rows:
            my_list.append(row[0])

        return render_template('template.html',  results=my_list)
    except Exception as e:
        print(e)
        return []
```

### Create Contacts

#### Implementation of `/createcontactform` endpoint

``` python

@app.route('/contactform')
def contactform():
   return render_template('contactform.html')

```


#### Implementation of `/create_contact` endpoint. 

``` python

@app.route('/create_contact', methods=['POST','GET'])
def create_contact():

    try:
        if request.method == "POST":
            first_name = request.form["first-name"]
            last_name = request.form["last-name"]
            email = request.form["email"]

            app.logger.info(first_name)
            statement = "insert into salesforce.contact(firstname, 
                        lastname, email) values ('" \
                + first_name + "','" + last_name + "','" + email + "');"
            cur.execute(statement)
            conn.commit()
            errors = []
            return render_template('result.html', errors=errors, 
                                   firstname=first_name,
                                   lastname=last_name)
    except Exception as e:
        print(e)
        return []
```

    
## Deploying to Heroku

Before moving on, create a [Heroku](https://signup.heroku.com/) account and run `$ heroku login` command to login to your created heroku account.

``` bash

$ heroku create
$ git push heroku master
$ heroku open
```
## Add PostgreSQL Add-On

Add Postgress Add-On as shown below.

  ``` bash
  $ heroku addons:create heroku-postgresql:hobby-dev
  ```
## Add Heroku Connect Add-On

Configure Heroku Connect Add-On. Command below configures Herok-Connect Add-On to the application.

  ``` bash
  $ heroku addons:create herokuconnect
  ```

## Configure Heroku Connect Add-On

* Setup Connection

   <img src="images/setup-connection.png" width="90%" height="80%"> 

* Enter Schema Name : This is the schema name underwhich database will be created.

   <img src="images/enter-schemaname.png" width="90%" height="80%"> 

* Trigger OAuth 

   <img src="images/trigger-oauth.png" width="90%" height="80%">  

* Enter Salesforce.com developer account credentials

   <img src="images/sfaccountdetails.png" width="40%" height="50%"> 

* Create Mappings

   <img src="images/create-mappings.png" width="90%" height="80%">   

* Create Mappings Contacts : Choose the fields in Salesforce Schema which need to be mapped to Postgres Database in the application.

   <img src="images/create-mapping-contacts.png" width="90%" height="80%">  

* **Write Enable** : Make sure you enable `Write to Salesforce any updates to your database` check box

* Explore Contacts in the Dashboard

   <img src="images/contacts-explorer.png" width="90%" height="80%"> 

## Home Page

   <img src="images/contacts-home.png" width="50%" height="50%"> 
  
## Contact List

  Browse to URL `http://{your-app-name}.herokuapp.com/contacts` to see the list of contact names.
  <img src="images/contacts-list.png" width="50%" height="50%"> 

## Create a New Contact
   
  Browse to URL `http://{your-app-name}.herokuapp.com/createcontactform` to see the list of contact names.

  <img src="images/create-contacts.png" width="50%" height="50%"> 

## Show Contacts Locally

  Configure the DATABASE_URL in the local environment

  ``` bash
    $ heroku config

    === fast-sands-40695 Config Vars
    DATABASE_URL:      postgres://<user_name>:<password>@<ipaddress>.compute-1.amazonaws.com:5432/<database_name>
    HEROKUCONNECT_URL: DATABASE_URL:salesforce
  ```

  Export DATABASE_URL

  ``` bash
    $ export DATABASE_URL=postgres://<user_name>:<password>@<ipaddress>.compute-1.amazonaws.com:5432/<database_name>
  ```

  Open the following URL :code:`http://localhost:5000/contacts` you should be able see the contacts.

## Executing using PyCharm

* Install the [Pycharm](https://www.jetbrains.com/pycharm/download/). And add your directory as a project to PyCharm.

  <img src="images/pycharm1.png" width="90%" height="80%">

* Set the Environment variable as `DATABASE_URL=postgres://<user_name>:<password>@<ipaddress>.compute-1.amazonaws.com:5432/<database_name>` by following the path File > Default Settings
  
  <img src="images/pycharm3.png" width="90%" height="80%">
  
* Click on `app.py` and run the app.

  <img src="images/pycharm2.png" width="90%" height="80%">

  Open the following URL `http://localhost:5000/contacts` you should be able see the contacts.

## Summary

  In this workshop we learnt how to configure a Python Flask Application to work with Heroku Connect. We used Psycopg2 driver for talking to the PostgreSQL database deployed on Heroku.

  

  
  

