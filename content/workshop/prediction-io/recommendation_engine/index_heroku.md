+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Build a Recommendation Engine with Prediction IO : Heroku"

+++

1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Source Code](#source-code)
4. [Step 1 Clone the Source code ](#step-1-clone-the-source-code)
5. [Step 2 Create a Heroku App](#step-2-create-a-heroku-app)
6. [Step 3 Deploy Event Server to Heroku](#step-3-deploy-event-server-to-heroku)
7. [Step 4 Create a new app](#step-4-create-a-new-app) 
8. [Step 5 Populate Event Server with Events](#step-5-populate-event-server-with-events)
9. [Step 6 Deploy Recommendation Engine](#step-6-deploy-recommendation-engine)
10. [Step 7 Configure the Heroku app](#step-7-configure-the-heroku-app)
11. [Step 8 Increase Heap size for Java VM](#step-8-increase-heap-size-for-java-vm)
12. [Step 9 Train the Engine](#step-9-train-the-engine) 
13. [Step 10 Predict](#step-10-predict)



## Introduction

In this workshop you will learn how to use Prediction IO Machine Learning library to build a recommendation engine based on Alternative Least Square Algorithm. Prediction IO uses Spark MLlib's implementation and provide convenient APIs and REST endpoints to get the infrastructure up and running fast.

<img src="/workshop/prediction-io/recommendation_engine/images/recommendation_engine_local.png" width="80%" height="80%">

## Prerequisites

* Heroku Account
* Heroku CLI (git is part of Heroku CLI)
* curl

Heroku account with Credit Card is required for two Dynos to run simultaneously

## Source Code

Source code of this workshop resides in two repos listed below

* https://github.com/rajdeepd/pio-eventserver-heroku
* https://github.com/rajdeepd/pio-engine-heroku

**pio-eventserver-heroku** : Provdes storage for events being generated based on which we want to create our training model.

**pio-engine-heroku** : Engine which wraps the ALS Algorithm implementation and provides APIs to create a model, train it and use it to make prediction.

We will be using PostgreSQL database for this workshop.

## Step 1 Clone the Source code 

Clone the source code

``` bash

$ git clone https://github.com/rajdeepd/pio-eventserver-heroku

$ git clone https://github.com/rajdeepd/pio-engine-heroku

```
## Step 2 Create a Heroku App

*Note: You need to change `rd-pio-eventserver-1` to your own unique app name*

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

## Step 3 Deploy Event Server to Heroku

``` bash

$ git push heroku master

```

Output

```
remote:        [info] Done updating.
remote:        [info] Compiling 1 Scala source to /tmp/scala_...
remote:        [success] Total time: 42 s, completed Aug 25, 2016 9:59:56 AM
remote:        [info] Wrote scala-2.10/pio-eventserver-heroku_2.10-0.1-SNAPSHOT.pom
remote:        [info] Packaging pio-eventserver-heroku_2.10-0.1-SNAPSHOT.jar ...
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
DATABASE_URL: postgres://rdatjvbvdwqvyq:nNL9b1cnjoQt8hCcQumEMahrmL@ec2-54-243-208-195.compute-1.amazonaws.com:5432/d8spomhdp00n03
```

## Step 4 Create a new app 

Prediction IO tracks events, ML engine based on App ID. We will create a new app and tie events to this ID as well the ML engine which will be trained later

``` bash

$ heroku run console app new MyApp1
```

```
Running `console app new MyApp1` attached to terminal... up, run.5174
[INFO] [App$] Initialized Event Store for this app ID: 1.
[INFO] [App$] Created new app:
[INFO] [App$]       Name: MyApp1
[INFO] [App$]         ID: 1
[INFO] [App$] Access Key: 2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O

```
### Set Environment variable

#### Linux, Mac OS X

``` bash

$ export ACCESS_KEY=2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O

```

##### Windows

``` bash

$ set ACCESS_KEY=2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O

```

## Step 5 Populate Event Server with Events

Please change heroku app name from CHANGEME to the actual value you gave earier in the URL for all the commands listed below. 

#### Linux, Mac OS X

``` bash

for i in {1..5}; do curl -i -X POST http://CHANGEME.herokuapp.com/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"user\", \"entityId\" : \"u$i\" }"; done

for i in {1..50}; do curl -i -X POST http://CHANGEME.herokuapp.com/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"item\", \"entityId\" : \"i$i\", \"properties\" : { \"categories\" : [\"c1\", \"c2\"] } }"; done

for i in {1..5}; do curl -i -X POST http://CHANGEME.herokuapp.com/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"view\", \"entityType\" : \"user\", \"entityId\" : \"u$i\",  \"targetEntityType\" : \"item\", \"targetEntityId\" : \"i$(( ( RANDOM % 50 )  + 1 ))\" }"; done

```

#### Windows 

``` bash

for /L %a IN (1,1,5) DO (
  curl -i -X POST http://rd-pio-eventserver-t1.herokuapp.com/events.json?accessKey=%ACCESS_KEY% -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"user\", \"entityId\" : \"u%a\" }"
)


for /L %a IN (1,1,50) DO (
  curl -i -X POST http://rd-pio-eventserver-t1.herokuapp.com/events.json?accessKey=%ACCESS_KEY% -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"item\", \"entityId\" : \"i%a\", \"properties\" : { \"categories\" : [\"c1\", \"c2\"] } }"
)

for /L %a IN (1,1,5) DO (
     curl -i -X POST http://rd-pio-eventserver-t1.herokuapp.com/events.json?accessKey=%ACCESS_KEY% -H "Content-Type: application/json" -d "{ \"event\" : \"view\", \"entityType\" : \"user\", \"entityId\" : \"u1\",  \"targetEntityType\" : \"item\", \"targetEntityId\" : \"i%a\" }"
)

```

### Step 5.1 Check the Events Inserted in a Browser

``` bash

http://rd-pio-eventserver-1.herokuapp.com/events.json?accessKey=2Evbo5hiUiXXXCu_uB-gK1Q3EiT2N8nGd1-AGY5hjrsQ3PonJCdwP1YZ5WN5519O&limit=100

```

<img src="/workshop/prediction-io/recommendation_engine/images/pio-events-screenshot.png" width="100%" height="100%">

## Step 6 Deploy Recommendation Engine 

*Note: You need to change `rd-pio-engine-1` to your own unique app name*

``` bash
$ cd pio-engine-heroku
$ heroku create rd-pio-engine-1
$ git push heroku master

```

### Step 6.1 : Remove existing AddOn

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

$ heroku addons:remove postgresql-pointy-19292

```

Output

``` 

WARNING: `heroku addons:remove` has been deprecated. Please use `heroku addons:destroy` instead.

 !    WARNING: Destructive Action
 !    This command will affect the app: rd-pio-engine-1
 !    To proceed, type "rd-pio-engine-1" or re-run this command with --confirm rd-pio-engine-1

> rd-pio-engine-1
Destroying postgresql-pointy-19292 on rd-pio-engine-1... done, (free)
Removing vars for DATABASE from rd-pio-engine-1 and restarting... done, v5

```

### Step 6.2 Configure DATABASE_URL to point to Event Server DB

``` bash

$ heroku config:set DATABASE_URL=postgres://rdatjvbvdwqvyq:nNL9b1cnjoQt8hCcQumEMahrmL@ec2-54-243-208-195.compute-1.amazonaws.com:5432/d8spomhdp00n03

```

## Step 7 Configure the Heroku app

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

## Step 8 Increase Heap size for Java VM

``` bash

$ heroku config:set JAVA_OPTS="-Xmx512m"

```
## Step 9 Train the Engine

In this step we will train the Recommendation Engine based on the Events inserted above. Code listed below is the core training method called inside Prediction IO Servier

``` scala

val m = ALS.trainImplicit(
      ratings = mllibRatings,
      rank = ap.rank,
      iterations = ap.numIterations,
      lambda = ap.lambda,
      blocks = -1,
      alpha = 1.0,
      seed = seed)

    new ALSModel(
      productFeatures = m.productFeatures.collectAsMap.toMap,
      itemStringIntMap = itemStringIntMap,
      items = items
    )

**Optional Step**

```
If you are running free dynos makes sure you scale down the web dynos before training

``` bash

$ heroku ps:scale web=0 train=0

```

Now run the training command

``` bash

$ heroku run train

```
Output

```

[INFO] [Engine$] ALSModel does not support data sanity check. Skipping check.
[INFO] [Engine$] EngineWorkflow.train completed
[INFO] [Engine] engineInstanceId=efb28115-5007-4356-a45c-cab9b7b1da6f
[INFO] [CoreWorkflow$] Inserting persistent model
[INFO] [CoreWorkflow$] Updating engine instance
[INFO] [CoreWorkflow$] Training completed successfully.
[INFO] [ServerConnector] Stopped ServerConnector@18578491{HTTP/1.1}{0.0.0.0:4040}

```

Bring back the Web dyno (for setups using free dynos)

``` bash

heroku ps:scale web=1 train=0


```


Check the Recommendation Engine running in the browser

<img src="/workshop/prediction-io/recommendation_engine/images/pio-engine-screenshot.png" width="100%" height="100%">

## Step 10 Predict

Items similar to i3

####  Linux, Mac OSX

``` bash
$ curl -H "Content-Type: application/json" -d '{ "items": ["i3"], "num": 4 }' \
   -k http://rd-pio-engine-1.herokuapp.com/queries.json

```
#### Windows

``` bash
curl -H "Content-Type: application/json" ^
    -d "{\"items\": [\"i3\"], \"num\": 4 }" ^
    -k http://rd-pio-engine-1.herokuapp.com/queries.json

```
Response will be similar to the listing below

``` json

{"itemScores":
	[
		{
			"item":"i44","score":0.2805472425881496
		},
		
		{
			"item":"i41","score":0.14458527026450552
		}
	]
}

```


