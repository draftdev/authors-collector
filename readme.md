# Draft.dev Author Database

This project contains the Draft.dev Author Database collection code that is run on n8n.io

## Deploying to Production
- Set up a new DO droplet with Node 12+ installed
- SSH into the new server
- Clone this repo into the web directory: `git clone https://github.com/draftdev/authors-collector.git /var/www/html` 
- Install dependencies: `npm i`
- Create a `.config` file and copy the base config into it (see below).
- Run n8n using pm2: `` ... `N8N_PORT=3000 npm start`
- View the app and copy workflows into it:  

## Base Config
```json
{
	"security": {
		"basicAuth": {
			"active": true,
			"user": "...",
			"password": "..."
		}
	}
}
```

## Upgrading in Production
TBD

## Database Schema
TBD

## Workflows
TBD
