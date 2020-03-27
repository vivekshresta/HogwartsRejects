# HogwartsRejects
Weather Forecasting API

Wiki: https://github.com/airavata-courses/HogwartsRejects/wiki 

#### Maintained by
##### Vivek Shresta Bandaru - vivekshrestabandaru@gmail.com
##### Gopi kiran - gopikiran292@gmail.com
##### Rishabh Gajra - rgrishigajra@gmail.com

## System Architecture
This project has 7 microservices and each microservice has its own master branch.
![Image description](https://cdn.discordapp.com/attachments/394719662086291466/679153099763351562/image0.jpg)

## UI Front End

The user interface is made in React Js which creates a virtual DOM different than the browser DOM to show the entire website.

## Nginx

The user interface is served by Nginx which is used to host the front end created by the React JS framework. Nginx in future can be used to route to multiple copies of API Gateway and act as a load balancer.

## API Gateway

API gateway takes care of routing the requests from UI to the backend microservices. It adds a level of masking and abstraction.

## Data Retrieval

This service retrieves current weather data from an api and provides data to front end to show on landing page.

## User Management

User Management microservice takes care of login, signup and the verification of each request before api gateway produces to a kafka topic making sure every request is from an authenticated user.

By generating a JWT token, the username of the user is never exposed.
This way, even if the username is exposed via logs, the possibility of session hijacking is drastically decreased.

User Management also takes care of decrypting the JWT token and setting the username in response. This way usermame is passed internally across the other microservices for mapping the jobs(or user queries) with the user.

Since, login should be synchronous, we exposed a REST endpoint for login, signup and verify. Upon successfull validation, the api returns the jwt or the username respectively which is cosumed by the API gateway.

The DB used for this microservice is PostgresSQL.


## Session Management 

Session Management microservices takes care of tracking the user queries and their statuses.
Basically, once a user queries a job, the session management creates an entry for that. Any change in status of the job will be consumed by the SessionManagement and will immediately be stored in MongoDB.

This is achieved through Kafka. SessionManagement has its own TOPIC to which all the microservices send messages about their status.

Session Management also takes care of providing the user the list of all his/her queries upon request.

Currently, this can be done either via a RestEndpoint or through Kafka.

The DB used for this microservice is MongoDB.

## Modeling service

This service majorly takes in user query request from the front end and gets that file from the Amazon data service, it also models the data from the file and stores it in A url. This url is image hosting service provided by imgr

## Post Processing

Once an image is created, it is hosted on cloud. For now, post processing keeps track of the user's jobs and the image url on cloud. When the user requests for his history of queries, SessionManagement lists all the user queries. Upon selecting a query, Post-Processing fetches the appropriate image url and sends it to API Gateway.

Currently, this can be done either via a RestEndpoint or through Kafka.

The DB used for this microservice is MongoDB.
