+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Text Classification : Spam Detection Engine using Logistical Regression : Heroku"

+++

## Introduction

In this workshop you will learn how to use PredictionIO Machine Learning library to build a Spam Detection Engine using Classification Technique of Logisitical Regression. PredictionIO uses Spark MLlib's implementation and provide convenient APIs and REST endpoints to get the infrastructure up and running fast.


## Pre-requisities

* git command line
* JDK 1.8.x or above
* Heroku Account 
* Heroku CLI

Heroku account with Credit Card is required for two Dynos to run simultaneously

## Source Code

Source code of this workshop resides in two repos listed below

* https://github.com/rajdeepd/pio-eventserver-heroku
* https://github.com/rajdeepd/pio-engine-textclassfication-heroku
* https://github.com/rajdeepd/pio-upload-data

**pio-eventserver-heroku** : Provides storage for events being generated based on which we want to create our training model.

**pio-engine-textclassfication-heroku** : Engine which wraps the [Logisitic Regression Algorithm](https://en.wikipedia.org/wiki/Logistic_regression) implementation and provides APIs to create a model, train it and use it to make prediction.

We will be using PostgreSQL database for this workshop.

## Step 1 : Clone the Source code 

Clone the source code

``` bash

$ git clone https://github.com/rajdeepd/pio-eventserver-heroku

$ git clone https://github.com/rajdeepd/pio-engine-textclassfication-heroku

$ git clone https://github.com/rajdeepd/pio-upload-data

```
## Step 2 Create a Heroku App

First we will create a new Heroku app locally.

Note : Please replace `rd-pio-eventserver-1` with the name of your app

``` bash
$ cd pio-eventserver-heroku
$ heroku create rd-pio-eventserver-1

```
Output

``` bash

Creating rd-pio-eventserver-1... done, stack is cedar-14
https://rd-pio-eventserver-1.herokuapp.com/ | 
https://git.heroku.com/rd-pio-eventserver-1.git
Git remote heroku added

```

Check git remote

``` bash
$ git remote -v

```
```
heroku	https://git.heroku.com/rd-pio-eventserver-1.git (fetch)
heroku	https://git.heroku.com/rd-pio-eventserver-1.git (push)
origin	https://github.com/rajdeepd/pio-eventserver-heroku (fetch)
origin	https://github.com/rajdeepd/pio-eventserver-heroku (push)

```

## Deploy Event Server to Heroku

``` bash

$ git push heroku master

```

Output

```
remote:        [info] Done updating.
remote:        [info] Compiling 1 Scala source to /tmp/scala_...
remote:        [success] Total time: 42 s, completed Aug 25, 2016 9:59:56 AM
remote:        [info] Wrote 
                 scala-2.10/pio-eventserver-heroku_2.10-0.1-SNAPSHOT.pom
remote:        [info] Packaging pio-eventserver-heroku_2.10-0.1-SNAPSHOT.jar 
remote:        [info] Done packaging.
remote:        [success] Total time: 2 s, completed Aug 25, 2016 9:59:58 AM
remote: -----> Dropping ivy cache from the slug
remote: -----> Dropping sbt boot dir from the slug
remote: -----> Dropping compilation artifacts from the slug
remote: -----> Discovering process types
remote:        Procfile declares types -> console, web
remote: 
remote: -----> Compressing...
remote:        Done: 183.1M
remote: -----> Launching...
remote:        Released v4
remote:        https://rd-pio-eventserver-1.herokuapp.com/ deployed to Heroku
remote: 
remote: Verifying deploy.... done.
To https://git.heroku.com/rd-pio-eventserver-1.git
 * [new branch]      master -> master

```

Check the `DATABASE_URL`

``` bash
$ heroku config
=== rd-pio-eventserver-1 Config Vars
DATABASE_URL: postgres://username:password@ec2-54-243-208-195.compute-1.amazonaws.com:5432/d8spomhdp00n03
```

## Create a new app 

PredictionIO tracks events, ML engine based on App ID. We will create a new app and tie events to this ID as well the ML engine which will be trained later

``` bash

$ heroku run console app new MyAppLR

```

```
Running `console app new MyAppLR` attached to terminal... up, run.4659
[INFO] [App$] Initialized Event Store for this app ID: 2.
[INFO] [App$] Created new app:
[INFO] [App$]       Name: MyAppLR
[INFO] [App$]         ID: 2
[INFO] [App$] Access Key: 2eNMw5lydFtFl4uT8l5oARd48VDxJz9sIEUuigshZJHttReO5lpiNDeZwELVV3_7
WARNING: Toolbelt v3.43.9 update available.

```
Set Environment variable ACCESS_KEY

``` bash

$ export ACCESS_KEY=<ACCESS_KEY>

```

## Populate Event Server with Events

We will use a Scala Application cloned from [https://github.com/rajdeepd/pio-upload-data](https://github.com/rajdeepd/pio-upload-data) to upload data. It will make a Http Post request and upload email and stop word data.

Sample email event

``` json

{
  "eventTime": "2015-06-08T16:45:00.595+0000",
  "entityId": 2,
  "properties": {
    "text": " some spam text",
    "label": "spam"
  },
  "event": "e-mail",
  "entityType": "content"
}

```


1. Copy application.conf.sample to application.conf as shown below

    ``` bash

    $ cd pio-upload-data
    $ cp pio-upload-data/src/main/resources/application.conf.sample \
       pio-upload-data/src/main/resources/application.conf
      
    ```

2. Open `application.conf` file

    ``` bash

    pio {
      access_token = "TODO"
      app_name = "TODO"
      host = "localhost:7070"
    }

    ```
    
    Update with appropriate values as shown below


    ``` bash
    	
    pio {
      access_token = "2eNMw5lydFtFl4uT..Jz9sIEUuigshZJHttReO5lpiNDeZwELVV3_7"
      app_name = "MyTextLR"
      host = "rd-pio-eventserver-1.herokuapp.com"
    }

    ```

3. Execute Upload

    We will upload 100 emails and 300+ stopwords (in real use case your email data will be much larger)

    
        $ cd ~/pio-upload-data
        $ ./sbt "runMain UploadEmails"
        $ ./sbt "runMain UploadStopWords"
   
Your output will be similar to listing below

```
98
HttpResponseProxy{HTTP/1.1 201 Created [Connection: .. charset=UTF-8,Content-Length: 46,Chunked: false]}}
99
HttpResponseProxy{HTTP/1.1 201 Created [Connection: .. charset=UTF-8,Content-Length: 46,Chunked: false]}}
100
Completed uploads
[success] Total time: 96 s, completed 31 Aug, 2016 12:37:25 PM

```

### Check the Events Inserted in a Browser

``` bash

http://rd-pio-eventserver-1.herokuapp.com/events.json?accessKey=MYACCESSKEY&limit=100

```

<img src="/workshop/prediction-io/recommendation_engine/images/pio-events-screenshot.png" width="100%" height="100%">

## Deploy Recommendation Engine 

``` bash

$ heroku create rd-pio-engine-text-class
$ git push heroku master

```

### Remove existing AddOn

``` bash
$ heroku addons
```

```
=== Resources for rd-pio-engine-1
Plan                         Name                     Price
---------------------------  -----------------------  -----
heroku-postgresql:hobby-dev  postgresql-pointy-19292  free

```

``` bash

$ heroku addons:destroy postgresql-pointy-19292

```

Output

``` 

 !    WARNING: Destructive Action
 !    This command will affect the app: rd-pio-engine-1
 !    To proceed, type "rd-pio-engine-1" or re-run this command with --confirm rd-pio-engine-1

> rd-pio-engine-1
Destroying postgresql-pointy-19292 on rd-pio-engine-1... done, (free)
Removing vars for DATABASE from rd-pio-engine-1 and restarting... done, v5

```

### Configure DATABASE_URL to point to Event Server DB

``` bash

$ heroku config:set DATABASE_URL=postgres://username:password@ec2-54-243-208-195.compute-1.amazonaws.com:5432/d8spomhdp00n03

```

## Configure the Heroku app: 

``` bash 
heroku config:set ACCESS_KEY=<YOUR APP ACCESS KEY> APP_NAME=<APP NAME> EVENT_SERVER_IP=<YOUR EVENT SERVER HOSTNAME> EVENT_SERVER_PORT=80
```

Example

```
heroku config:set ACCESS_KEY=2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O APP_NAME=MyApp1 \
  EVENT_SERVER_IP=rd-pio-eventserver-1.herokuapp.com \
  EVENT_SERVER_PORT=80
```

Output

```
Setting config vars and restarting rd-pio-engine-1... done, v6
ACCESS_KEY:        2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O
APP_NAME:          MyApp1
EVENT_SERVER_IP:   rd-pio-eventserver-1.herokuapp.com
EVENT_SERVER_PORT: 80

```

## Increase Heap size for Java VM

``` bash

$ heroku config:set JAVA_OPTS="-Xmx512m"

```
## Train

In this step we will train the Recommendation Engine based on the Events inserted above.

``` bash

$ heroku run train

```
Output

```
Running `train` attached to terminal... up, run.6559
Picked up JAVA_TOOL_OPTIONS: -Dfile.encoding=UTF-8
[WARN] [NativeCodeLoader] Unable to load native-hadoop library for your
        platform... using builtin-java classes where applicable
[INFO] [Remoting] Starting remoting
[INFO] [Remoting] Remoting started; listening on addresses :
       [akka.tcp://sparkDriver@172.16.194.10:48050]
[INFO] [Server] jetty-8.y.z-SNAPSHOT
[INFO] [AbstractConnector] Started SocketConnector@0.0.0.0:33483
[INFO] [Server] jetty-8.y.z-SNAPSHOT
[INFO] [AbstractConnector] Started SelectChannelConnector@0.0.0.0:4040
[INFO] [Engine$] EngineWorkflow.train
[INFO] [Engine$] DataSource: 
             org.template.textclassification.DataSource@1a47a1e8
[INFO] [Engine$] Preparator: org.template.textclassification.Preparator@3811510
[INFO] [Engine$] AlgorithmList: List(org.template.textclassification.LRAlgorithm@748f93bb)
[INFO] [Engine$] Data sanity check is on.
[INFO] [Engine$] org.template.textclassification.TrainingData supports 
                 data sanity check. Performing check.Observation 1 label: 1.0
Observation 2 label: 1.0
Observation 3 label: 1.0
Observation 4 label: 1.0
Observation 5 label: 1.0

[INFO] [Engine$] org.template.textclassification.PreparedData 
                 does not support data sanity check. Skipping check.                                      
INFO] [Engine$] org.template.textclassification.LRModel does not 
                support data sanity check. Skipping check.
[INFO] [Engine$] EngineWorkflow.train completed
[INFO] [Engine] engineInstanceId=7e2e9a86-a17c-4ac7-bfde-9941430e85dd

```

Check the Classification Engine running in the browser

<img src="/workshop/prediction-io/text_classification/images/pio-text-engine-heroku-screenshot.png" width="100%" height="100%" alt="engine image">

## Predict


``` bash
$ curl -H "Content-Type: application/json" -d '{ "text":"Earn extra cash!" }' \
       http://rd-pio-engine-text-class.herokuapp.com/queries.json


```

   ``` json 
{"category":"not spam","confidence":0.7219493232910391}

   ```



