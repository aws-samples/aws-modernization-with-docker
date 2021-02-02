+++
title = "Module 3: Secure your Containers with Docker and AWS Secrets Manager"
chapter = true
weight = 42
+++

# Securing your containers with AWS Secrets Manager

1. Refactor backend code to connect to MongoDB
    1. Open path/to/github/repo/backend
    2. Open the server.js file and add update the code:
```
const ronin     = require( 'ronin-server' )
const mocks     = require( 'ronin-mocks' )
const database	= require( 'ronin-database' )

async function main() {

	try {
		const dbConnectionString = process.env.DB_CONN_STR || 'localhost:27017/crud-datastore'
		await database.connect( process.env.DB_CONN_STR )

    const server = ronin.server({
			port: process.env.PORT || 8080
		})

		server.use( '/services/', mocks.server( server.Router() ) )

    const result = await server.start()
    console.info( result )

	} catch( error ) {
		console.error( error )
	}

}

main()
```
2. Change to private repo in hub
    1. Navigate to hub.docker.com
    2. Select the `<hubaccount>/crud-frontend`
    3. Click settings
    4. Click “Make Private” and type the name of your repo
    5 .Repeat steps 1-4 for the `<hubaccount>/crud-backend`
3. Setup a Volume to persist data
4. 
5. Test locally
    1. Switch to default context
        - `docker context use default`
    2. Start application with docker-compose
        - `docker-compose up --build`
    3. Open browser to http://localhost
    4. Enter an entity name:
        - Widgets
    5. Click create new record
    6. Enter JSON for the new widget
        - `{ “name”: “This is a widget” }`
    7. Click save
    8. Observe the new widget record in the results below.

