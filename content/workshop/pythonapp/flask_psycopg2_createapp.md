# Flask and psycopg2 : New App

This article shows how to **Create** and **Run** a Python app with psycopg2 which uses PostgreSQL based Heroku Connect

Figure 1 show the  how HerokuConnect Add-On interacts with Heroku Postgres and force.com behind the scenes
Make sure you have Python installed.  Also, install the [Heroku Toolbelt](https://toolbelt.heroku.com/)

## Install Virtual Environment

Create a folder `flask-psycopg2-sample` and install a virtualenvironment in it.

  ```
    $ mkdir flask-psycopg2-sample
    $ cd flask-psycopg2-sample
    $ virtualenv venv
    $ source venv/bin/activate
  ```

Install Dependencies

  `$ pip install flask gunicorn`
  
## Creating a Simple Flask App

1. First Create a base Flask app with simple REST endpoint/ in a file `app.py` in the folder created above.
  
  ```python
  from flask import Flask
	app = Flask(__name__)

	@app.route('/')
	def hello_world():
	    return 'Hello World!'

	if __name__ == '__main__':
	    app.run()
	```
2. Run the app using the following command
 
  ` $ python app.py`

    Your app should now be running on [localhost:5000](http://localhost:5000)

## Initialize git

Initialize the git repository as shown by commands below.

  ```
    $ git init
    $ git add .
    $ git commit -m "initial commit"
  ```
## Create a Requirements File

  `$ pip freeze > requirements.txt`
  
## Create a Procfile

Create a file name `Procfile` in the root of the app and add following content. This specifies that the app uses a `web` dyno with `gunicorn` as http server.

  `web: gunicorn app:app --log-file -`
  
## Deploying to Heroku

  ```
    $ heroku create
    $ git push heroku master
    $ heroku open
  ```
## Add PostgreSQL Add-On

Add Postgress Add-On as shown below

  `$ heroku addons:create heroku-postgresql:hobby-dev`

## Add Heroku Connect Add-On

Configure Heroku Connect Add-On. Command below configures Herok-Connect Add-On to the application.

  `$ heroku addons:create herokuconnect`

## Configure Herok Connect Add-On

1. Setup Connection
2. Enter Schema Name : This is the schema name underwhich database will be created.
3. Trigger OAuth 
4. Enter Salesforce.com developer account credentials
5. Create Mappings
6. Create Mappings Contacts : Choose the fields in Salesforce Schema which need to be mapped to Postgres Database in the application.
7. Explore Contacts in the Dashboard

## Add Code for contacts endpoint 

  First Add following lines which configure Connection object conn to PostgreSQL Database.
  
  ```python
    url = urlparse.urlparse(os.environ.get('DATABASE_URL'))
    db = "dbname=%s user=%s password=%s host=%s " % (url.path[1:], url.username, url.password, url.hostname)
    schema = "schema.sql"
    conn = psycopg2.connect(db)
    cur = conn.cursor()
  ```
  
  Add code for the Getting the Contacts.
  
  ```python
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
      	  print e
      	  return []
```
Complete Code listing

  ```python
    import os
    import psycopg2
    from flask import Flask, render_template
    import urlparse
    from os.path import exists
    from os import makedirs
    
    url = urlparse.urlparse(os.environ.get('DATABASE_URL'))
    db = "dbname=%s user=%s password=%s host=%s " % (url.path[1:], url.username, url.password, url.hostname)
    schema = "schema.sql"
    conn = psycopg2.connect(db)
    
    cur = conn.cursor()
    
    app = Flask(__name__)
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
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
        print e
        return []
    if __name__ == '__main__':
      app.run()
  ```
  
## Add Jinja Template 

  The code shown in previous section uses template.html file which is a Jinja template. Add this file under folder templates
  
  ```html
    <html>
      <head>
        <title>Flask Template Example</title>
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <link href="http://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css" rel="stylesheet" media="screen">
        <style type="text/css">
          .container {
            max-width: 500px;
            padding-top: 100px;
            }
        </style>
      </head>
      <body>
        <div class="container">
          <p>Contacts:</p>
          <ul>
            {% for r in results %}
            <li>{{r}}</li>
            {% endfor %}
          </ul>
        </div>
        <script src="http://code.jquery.com/jquery-1.10.2.min.js"></script>
        <script src="http://netdna.bootstrapcdn.com/bootstrap/3.0.0/js/bootstrap.min.js">
        </script>
      </body>
    </html>
```

## Update python packages

  `$ pip install psycopg2`
  
## Add Requirements file

  `$ pip freeze > requirements.txt`
  
## Create a Procfile

  Create a Procfile which will be used to identify the web dyno and type of runtime
  
	` web: gunicorn app:app --log-file=-`

## Update Changes in Heroku

  ```
    $ git add .
    $ git commit -m "Added code for contacts"
    $ git push heroku master
  ```
  
  Open the App again in Heroku
  
  `$ heroku open`
  
## Show Contacts

  Browse to URL `http://{your-app-name}.herokuapp.com/contacts` to see the list of contact names.
  
## Summary

  In this tutorial we learnt how to configure a Python Flask Application to work with Heroku Connect. We used Psycopg2 driver for talking to the PostgreSQL database deployed on Heroku.

  

  
  

