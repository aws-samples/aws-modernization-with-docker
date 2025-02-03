+++
title = "Step 3: Run Application Locally"
chapter = false
weight = 21
+++

## Run the application locally

Now that we have built the application locally, lets use Docker Compose to run our application locally. We will use `docker compose up` to start all the three services that we defined in our `docker-compose.yaml` file. We will use the [default context](https://docs.docker.com/engine/context/working-with-contexts/) to run the application locally.

Please note that when we talk about testing our application locally, this is in reference to our virtual workspace environment via Cloud9. You will see later in this section that instead of being able to test our application by going to http://localhost:3000 in Chrome or Firefox, that we will be using the `curl` command to test our application instead. 


![Docker](/images/running-application-locally.png)


```sh

$ docker compose up -d
[+] Running 6/6
 ⠿ Network "docker-compose-ecs-sample_frontnet"    Created  0.3s
 ⠿ Network "docker-compose-ecs-sample_backnet"     Created  0.3s
 ⠿ Network "docker-compose-ecs-sample_default"     Created  0.3s
 ⠿ Container docker-compose-ecs-sample_db_1        Started  0.8s
 ⠿ Container docker-compose-ecs-sample_backend_1   Started  1.7s
 ⠿ Container docker-compose-ecs-sample_frontend_1  Started  2.5s 

```

This step can take few minutes. You can run `docker compose logs` to view the logs

```
Admin:~/environment/docker-compose-ecs-sample (main) $ docker compose logs
backend_1  |  * Serving Flask app "hello.py"
backend_1  |  * Environment: production
backend_1  |    WARNING: This is a development server. Do not use it in a production deployment.
backend_1  |    Use a production WSGI server instead.
backend_1  |  * Debug mode: off
backend_1  |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
db_1       | 2021-03-26 17:38:51+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.19-1debian10 started.
db_1       | 2021-03-26 17:38:51+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
db_1       | 2021-03-26 17:38:51+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.19-1debian10 started.
db_1       | 2021-03-26T17:38:52.231725Z 0 [Warning] [MY-011070] [Server] 'Disabling symbolic links using --skip-symbolic-links (or equivalent) is the default. Consider not using this option as it' is deprecated and will be removed in a future release.
db_1       | 2021-03-26T17:38:52.231834Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.19) starting as process 1
db_1       | 2021-03-26T17:38:53.016895Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
db_1       | 2021-03-26T17:38:53.022888Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
db_1       | 2021-03-26T17:38:53.075577Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.19'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
db_1       | 2021-03-26T17:38:53.091186Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
```


Run the following in command line.

* Load the application

```sh
curl http://localhost:3000
```

* Insert records in database

```sh
curl http://localhost:3000/add/2/name2
curl http://localhost:3000/add/3/name3
curl http://localhost:3000/add/4/name4

```



Great, now that the application is up and running locally, lets now see how to seamlessly and without much changes migrate the same application on to AWS.

* Inspect the resources

List the volumes created using `docker volume ls` and inspect them using `docker volume inspect docker-compose-ecs-sample_db-data`
List the networks created using `docker network ls`

* Stop the application

Run `docker compose down` to stop the application.

```sh
$ docker compose down
[+] Running 6/6
 ⠿ Container docker-compose-ecs-sample_frontend_1  Removed  0.4s
 ⠿ Container docker-compose-ecs-sample_backend_1   Removed 10.4s
 ⠿ Container docker-compose-ecs-sample_db_1        Removed  2.2s
 ⠿ Network "docker-compose-ecs-sample_default"     Removed  0.2s
 ⠿ Network "docker-compose-ecs-sample_frontnet"    Removed  0.7s
 ⠿ Network "docker-compose-ecs-sample_backnet"     Removed  0.5s
 ```

## Summary 

In this module, we learned how to use Docker Compose to build our appliction locally and how we can add networking and storage components to our application by defining those resources in our docker-compose files. In the next module, we will be taking our application that we built and modernization it by deploying it to Amazon ECS and adding AWS specific resources through the same methodology used in this module. 