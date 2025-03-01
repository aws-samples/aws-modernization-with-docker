+++
title = "Step 3: Test Our Pipeline"
chapter = false
weight = 21
+++

In this section we will push a code change to our GitHub to demonstrate how CodePipeline orchestrates our deployment pipeline. We will make the following change to trigger our pipeline to run. Let's go ahead and add a route to our Flask application that returns "Hello World!" to demonstrate that our pipeline works. 

We will add a route named /test to our Flask application that returns "Hello World!" and the following code changes should be made to backend/hello.py
```
import os
from flask import Flask
import mysql.connector
import uuid
import os


class DBManager:
    def __init__(self):
        password_file = os.environ.get('MY_SQL_PASSWORD_FILE','/run/secrets/db-password')
        pf = open(password_file, 'r')
        database = os.environ.get('MY_SQL_DATABASE','example')
        host = os.environ.get('MY_SQL_HOST',"db")
        user = os.environ.get('MY_SQL_USER',"root")
        self.connection = mysql.connector.connect(
            user=user, 
            password=pf.read(),
            host=host, # name of the mysql service as set in the docker-compose file
            database=database,
            auth_plugin='mysql_native_password'
        )
        pf.close()
        self.cursor = self.connection.cursor()
    
    def populate_db(self):

        self.cursor.execute('DROP TABLE IF EXISTS blog')
        self.cursor.execute('CREATE TABLE blog (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, title VARCHAR(256))')
        self.cursor.execute("insert into blog values(NULL,'initial load')")
        
        self.connection.commit()

    def insert_records(self,id,name):
        data = (id,str(name))
        query = (
                "INSERT INTO blog(id,title)"
                "VALUES (%s, %s)"
                )
        self.cursor.execute(query,data)
        self.connection.commit()
    
    def query_titles(self):
        self.cursor.execute('SELECT title FROM blog')
        rec = []
        for c in self.cursor:
            rec.append(c[0])
        return rec


server = Flask(__name__)
conn = None

@server.route('/')
def listName():
    global conn
    if not conn:
        conn = DBManager()
        conn.populate_db()
        #conn.insert_records()
    rec = conn.query_titles()

    response = ''
    for c in rec:
        response = response  + '<div>   ' + c + '</div>'
    return response

@server.route('/add/<id>/<name>')
def addName(id,name):
    global conn
    if not conn:
        conn = DBManager(password_file='/run/secrets/db-password')
    conn.insert_records(id,name)
    rec = conn.query_titles()

    response = ''
    for c in rec:
        response = response  + '<div>   ' + c + '</div>'
    return response

@server.route('/test')
def hello():
    return "Hello World!"

if __name__ == '__main__':
    server.run()
```

Make sure to save the file and commit the changes to your GitHub repository using the following commands:
```
$ git add .
```
```
$ git commit -m "adding test route to Flask app"
```
```
$ git push
```

If you go to the CodePipeline console you should see the following if our code changes were deployed properly. 
![Docker](/images/codepipeline-success.png)

You will also notice that our pipeline zips up changes to our source code and puts that in our S3 bucket that is responsible for holding our artifact for our pipeline as seen below:

![Docker](/images/s3.png)

The final test is to browse to the `/test` route in our app to see that our change was successfully made. Copy and paste the following to see the change made to our application. 
```
docker compose ps

docke-LoadB-ZUJVKEFNFM4J-ce26ed3e7fa4dce7.elb.us-east-1.amazonaws.com:3000/test
```