+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Text Classification : Spam Detection Engine using Logistical Regression : Local"

+++

## Introduction

In this workshop you will learn how to use Prediction IO Machine Learning library to build a Spam Detection Engine using Classification Technique of Logisitical Regression. Prediction IO uses Spark MLlib's implementation and provide convenient APIs and REST endpoints to get the infrastructure up and running fast.

## Pre-requisities

* git command line
* JDK 1.8.x or above

## Source Code

Source code of this workshop resides in two repos listed below

* https://github.com/rajdeepd/pio-eventserver-heroku
* https://github.com/rajdeepd/pio-engine-textclassfication-heroku
* https://github.com/rajdeepd/pio-upload-data

**pio-eventserver-heroku** : Provdes storage for events being generated based on which we want to create our training model.

**pio-textclassfication-engine-heroku** : Engine which wraps the Logitical Regression Algorithm implementation and provides APIs to create a model, train it and use it to make prediction.

We will be using PostgreSQL database for this workshop.

## Step 1 : Clone the Source code 

Clone the source code

``` bash

$ git clone https://github.com/rajdeepd/pio-eventserver-heroku

$ git clone https://github.com/rajdeepd/pio-engine-textclassfication-heroku

$ git clone https://github.com/rajdeepd/pio-upload-data


```

## step 2 : Start the PostgreSQL server

Make sure PostgreSQL server is running locally

``` bash

$ sudo service postgresql status
9.3/main (port 5432): online
9.4/main (port 5433): online
9.5/main (port 5434): online

```
Create a pio role with password pio in PostgreSQL

``` 

```

## Step 3 : Run the Event Server

Use the following command to run the Event Server.

``` bash
source bin/env.sh && ./sbt run

```
## Step 4 : Create an Event Application

``` bash

source bin/env.sh && ./sbt \
  "runMain io.prediction.tools.console.Console app new MyAppText"

```
Output of the command above will generate information about accesskey. We will be using this Access Key later.

``` bash

[INFO] [App$] Initialized Event Store for this app ID: 4.
[INFO] [App$] Created new app:
[INFO] [App$]       Name: MyAppText
[INFO] [App$]         ID: 4
[INFO] [App$] Access Key: SOME_ACCESS_KEY

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
  1 | MyAppText | 


```

## Step 4 : Add Demo Data from pio-upload-data

We are going to upload emails and stopwords stored in json files.



``` bash

$ cd ~/pio-upload-data
$ ./sbt "runMain UploadEmails"
$ ./sbt "runMain UploadStopWords"


```

Check the data generated using the following curl command

```

http://localhost:7070/events.json?accessKey=SOME_ACCESS_KEY&limit=-1

```

<img src="/workshop/prediction-io/text_classification/images/pio-events-text-classification-local.png" width="100%" height="100%">

## Step 5 : Train the Model

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


     <img src="/workshop/prediction-io/text_classification/images/pio-engine-text-classification-local.png" width="100%" height="100%">

## Step 6 : Predict Spam vs Non Spam

   ``` bash

$ curl -H "Content-Type: application/json" -d '{ "text":"Earn extra cash!" }' \
    http://localhost:8000/queries.json

   ```
   
Response

   ``` json 
{"category":"not spam","confidence":0.7219493232910391}

   ```




