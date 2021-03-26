+++
title = "Step 3: Run Application Locally"
chapter = false
weight = 21
+++

## Run the application locally

Now that we have built the application locally, lets use Docker Compose to run our application locally. We will use `docker compose up` to start all the three services that we defined in our `docker-compose.yaml` file. We will use the [default context](https://docs.docker.com/engine/context/working-with-contexts/) to run the application locally. 


![Docker](/images/running-application-locally.png)


```

$ docker compose up

[+] Building 32.3s (5/5) FINISHED                                                                                                                                                  
[+] Running 6/5
 ⠿ Network "docker-compose-ecs-sample_backnet"   Created                                                                                                                                                                                                                   0.1s
 ⠿ Network "docker-compose-ecs-sample_frontnet"  Created                                                                                                                                                                                                                   0.0s
 ⠿ Volume "docker-compose-ecs-sample_db-data"    Created                                                                                                                                                                                                                   0.0s
 ⠿ docker-compose-ecs-sample_db_1                Created                                                                                                                                                                                                                   1.3s
 ⠿ docker-compose-ecs-sample_backend_1           Created                                                                                                                                                                                                                   0.1s
 ⠿ docker-compose-ecs-sample_frontend_1          Created                                                                                                                                                                                                                   0.0s
Attaching to docker-compose-ecs-sample_frontend_1, docker-compose-ecs-sample_backend_1, docker-compose-ecs-sample_db_1

```

This step can take few minutes, but when you see a message similar to following, it means the application is up and running locally.

```
Attaching to docker-compose-ecs-sample_frontend_1, docker-compose-ecs-sample_backend_1, docker-compose-ecs-sample_db_1
docker-compose-ecs-sample_db_1 | 2021-03-08 21:51:09+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.19-1debian10 started.
docker-compose-ecs-sample_db_1 | 2021-03-08T21:51:09.909683Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.19) initializing of server in progress as process 46
docker-compose-ecs-sample_backend_1 |  * Serving Flask app "hello.py"
docker-compose-ecs-sample_backend_1 |  * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
docker-compose-ecs-sample_db_1 | 2021-03-08T21:51:11.736216Z 5 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
docker-compose-ecs-sample_db_1 | 2021-03-08 21:51:14+00:00 [Note] [Entrypoint]: Database files initialized
docker-compose-ecs-sample_db_1 | 2021-03-08 21:51:14+00:00 [Note] [Entrypoint]: Starting temporary server
docker-compose-ecs-sample_db_1 | 2021-03-08T21:51:22.245078Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
```

{{% notice info %}}
You can optionally run `docker compose up -d` to run the same command in background. When you do that you can open Docker Desktop to visualize the logs.
{{% /notice %}}


Run the following on any browser to test that our application works.

* Insert records in database

```
curl http://localhost:3000/add/2/name2
curl http://localhost:3000/add/3/name3
curl http://localhost:3000/add/4/name4

```

* Retrieve records from database

```
curl http://localhost:3000
```

Great, now that the application is up and running locally, lets now see how to seamlessly and without much changes migrate the same application on to AWS.

* Inspect the resources

List the volumes created using `docker volume ls` and inspect them using `docker volume inspect docker-compose-ecs-sample_db-data`
List the networks created using `docker network ls`

* Stop the application

Hit `Cntrl + C` to stop the application. 
If you start your application using `docker compose up -d`, then you may run `docker compose down` to stop the application.

## Summary 

In this module, we learned how to use Docker Compose to build our appliction locally and how we can add networking and storage components to our application by defining those resources in our docker-compose files. In the next module, we will be taking our application that we built and modernization it by deploying it to Amazon ECS and adding AWS specific resources through the same methodology used in this module. 