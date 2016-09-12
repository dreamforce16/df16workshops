+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Build a Recommendation Engine with Prediction IO : Local"

+++

1. [Introduction](#introduction)
2. [Pre-requisities](#pre-requisities)
3. [Source Code](#source-code)
 * [Clone the Source code](#clone-the-source-code)
 * [Start the PostgreSQL 9.6 server](#start-the-postgresql-9-6-server)
 * [Run the Event Server](#run-the-event-server)
 * [Create an Event Application](#create-an-event-application)
 * [Add Demo Data using curl](#add-demo-data-using-curl)
 * [Train the Model](#train-the-model)
 * [Predict Recommended Items](#predict-recommended-items)


## Introduction

In this workshop you will learn how to use Prediction IO Machine Learning library to build a recommendation engine based on Alternative Least Square Algorithm. Prediction IO uses Spark MLlib's implementation and provide convenient APIs and REST endpoints to get the infrastructure up and running fast.

<img src="/workshop/prediction-io/recommendation_engine/images/recommendation_engine_local.png" width="80%" height="80%">

## Pre-requisities

* git command line tool
* JDK 1.8.x or above
* PostgreSQL 9.6 or above

## Source Code

Source code of this workshop resides in two repos listed below

* https://github.com/rajdeepd/pio-eventserver-heroku
* https://github.com/rajdeepd/pio-engine-heroku

**pio-eventserver-heroku** : Provdes storage for events being generated based on which we want to create our training model.

**pio-engine-heroku** : Engine wraps the ALS Algorithm implementation and provides APIs to create a model, train it and use it to make prediction.

We will be using PostgreSQL database for this workshop.

## Step 1 : Clone the Source code 

Clone the source code

``` bash

$ git clone https://github.com/rajdeepd/pio-eventserver-heroku
$ git clone https://github.com/rajdeepd/pio-engine-heroku

```

## Step 2 : Start the PostgreSQL 9.6 server

Make sure PostgreSQL server is running locally

``` bash

$ sudo service postgresql status
9.3/main (port 5432): online
9.4/main (port 5433): online
9.5/main (port 5434): online

```
Create a pio role with password pio in PostgreSQL

``` bash
$ psql

ubuntu=# CREATE USER pio WITH PASSWORD 'pio';

```

## Step 3 : Run the Event Server

Use the following command to run the Event Server.

``` bash
source bin/env.sh && ./sbt run

```
## Step 4 : Create an Event Application

``` bash

source bin/env.sh && ./sbt "runMain io.prediction.tools.console.Console app new MyApp1"

```
Output of the command above will generate information about accesskey. We will be using this Access Key later.

``` bash

[INFO] [App$] Initialized Event Store for this app ID: 4.
[INFO] [App$] Created new app:
[INFO] [App$]       Name: MyApp1
[INFO] [App$]         ID: 4
[INFO] [App$] Access Key: zFqcHJF...Tv-M61CjWcW6IA0IuqXEVRP_zKT3

```

You can see the pio app related data and meta data tables being created in PostgreSQL


``` bash
$ psql -h 127.0.0.1 -U pio -W
Password for user pio: 
psql (9.5.3, server 9.3.9)
SSL connection (protocol: TLSv1.2, cipher: ..., bits: 256, compression: off)
Type "help" for help.

pio=> \d+
                                  List of relations
 Schema |             Name             |   Type   | Owner |    Size    | 
--------+------------------------------+----------+-------+------------+-
 public | pio_event_1                  | table    | pio   | 16 kB      | 
 public | pio_meta_accesskeys          | table    | pio   | 16 kB      | 
 public | pio_meta_apps                | table    | pio   | 16 kB      | 
 public | pio_meta_apps_id_seq         | sequence | pio   | 8192 bytes | 
 public | pio_meta_channels            | table    | pio   | 8192 bytes | 
 public | pio_meta_channels_id_seq     | sequence | pio   | 8192 bytes | 
 public | pio_meta_engineinstances     | table    | pio   | 64 kB      | 
 public | pio_meta_enginemanifests     | table    | pio   | 16 kB      | 
 public | pio_meta_evaluationinstances | table    | pio   | 8192 bytes | 
 public | pio_model_models             | table    | pio   | 16 kB      | 
(11 rows)

pio=> 

```

``` bash

pio=> select * from pio_meta_apps;
 id |  name  | description 
----+--------+-------------
  1 | MyApp1 | 


```

## Step 5 : Add Demo Data using curl

``` bash
export ACCESS_KEY=<YOUR ACCESS KEY>
for i in {1..5}; do curl -i -X POST http://localhost:7070/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"user\", \"entityId\" : \"u$i\" }"; done

for i in {1..50}; do curl -i -X POST http://localhost:7070/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"\$set\", \"entityType\" : \"item\", \"entityId\" : \"i$i\", \"properties\" : { \"categories\" : [\"c1\", \"c2\"] } }"; done

for i in {1..5}; do curl -i -X POST http://localhost:7070/events.json?accessKey=$ACCESS_KEY -H "Content-Type: application/json" -d "{ \"event\" : \"view\", \"entityType\" : \"user\", \"entityId\" : \"u$i\",  \"targetEntityType\" : \"item\", \"targetEntityId\" : \"i$(( ( RANDOM % 50 )  + 1 ))\" }"; done

```

Check the data generated using the following curl command

```
http://localhost:7070/events.json?accessKey=&limit=-1
```

<img src="/workshop/prediction-io/recommendation_engine/images/localhost_events.png" width="100%" height="100%">

## Step 6 : Train the Model

1. Set your PredictionIO app's access key and app name in your env vars:
   
     ``` bash

     export ACCESS_KEY=<YOUR ACCESS KEY>
     export APP_NAME=<YOUR APP NAME>

     ```

    Note: These values come from the apps defined in your event server.

2. Train the app:
    
     ``` bash

     source bin/env.sh && ./sbt "runMain TrainApp"

     ```

3. Start the server:

    ``` bash

    source bin/env.sh && ./sbt "runMain ServerApp"

    ```

4. Check the status of your engine at `http://localhost:8000`


    <img src="/workshop/prediction-io/recommendation_engine/images/pio-engine-recommendation-local.png" width="100%" height="100%">

## Step 7 : Predict Recommended Items

Let us look at recommendation for other items based on a  item already bought by the user. Make sure by looking at the events in the event server that this item was bought, else recommended items will be empty list.

   ``` bash

$ curl -H "Content-Type: application/json" -d '{ "items": ["i3"], "num": 4 }' \
    http://localhost:8000/queries.json

   ```
   
Response

   ``` json 

{
  "itemScores":
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








