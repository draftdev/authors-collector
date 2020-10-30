# Draft.dev Author Database

This project contains the Draft.dev Author Database collection code that is run on n8n.io

## Deploying to Production
- Set up a new DO droplet with Node 12+ installed
- SSH into the new server
- Clone this repo into the web directory: `git clone https://github.com/draftdev/authors-collector.git /var/www/html` 
- Install dependencies: `npm i`
- Create a `.config` file and copy the base config into it (see below).
- Run n8n as the nodejs user using pm2: `sudo -u nodejs N8N_PORT=3000 N8N_CONFIG_FILES=.config pm2 start node_modules/.bin/n8n`
- View the app and copy workflows into it: `http://<DROPLET_IP_ADDRESS>`

## Base Config

The config adds basic auth to ensure people can't come in and mess with your workflows.

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

- Kill the node process: `sudo -u nodejs pm2 kill`
- Pull the latest code: `git pull`
- Install packages: `npm i`
- Start the node process again (see above).

## Database Schema

Updated 10/30/20

```postgresql
-- Install extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Sources table
create table sources
(
    id uuid default uuid_generate_v1() not null
        constraint sources_pk
            primary key,
    name varchar not null,
    rss_url varchar(1024) not null,
    url varchar(1024),
    pay_rate varchar(256),
    last_checked_at timestamp
);

create unique index sources_rss_url_uindex
    on sources (rss_url);

-- Posts table
create table posts
(
    id uuid default uuid_generate_v1() not null
        constraint posts_pk
            primary key,
    title varchar(1024),
    url varchar(1024) not null,
    description text,
    source_id uuid,
    author_id uuid,
    original_author varchar(256),
    original_topics json,
    created_at timestamp default now() not null,
    published_at timestamp
);

create unique index posts_url_uindex
    on posts (url);

-- Authors table
create table authors
(
    id uuid default uuid_generate_v1() not null
        constraint authors_pk
            primary key,
    name varchar(1024) not null,
    bio text,
    links json,
    created_at timestamp default now() not null
);

-- Topics tables
create table topics
(
    id serial
        constraint topics_pk
            primary key,
    name varchar(50) not null,
    description varchar(720)
);

create unique index topics_name_uindex
	on topics (name);

create table post_topics (
    post_id uuid REFERENCES posts (id) ON UPDATE CASCADE ON DELETE CASCADE,
    topic_id int REFERENCES topics (id) ON UPDATE CASCADE,
    CONSTRAINT post_topics_pkey PRIMARY KEY (post_id, topic_id)
);
```

Data Sources

- Sources include most [paying community writer programs](https://github.com/malgamves/CommunityWriterPrograms) as well as select Medium blogs.
- Topics extracted from [Stack Overflow](https://data.stackexchange.com/stackoverflow/query/1318327/get-all-tags-used-at-least-1000-times).

## Workflows

See the `./workflows` folder.
