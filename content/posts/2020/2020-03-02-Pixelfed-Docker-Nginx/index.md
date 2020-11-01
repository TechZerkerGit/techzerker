---
title: "Pixelfed with Docker and Nginx Reverse Proxy"
#subtitle: ""
date: 2020-03-02
draft: false
# images: []
tags: ["Linux","Open Source","Pixelfed","Instagram","Photography","FOSS","Self-Hosted","Docker"]
categories: ["Tech","Linux","Self-Hosted"]
---

As I have continued my expansion into self-hosting as well as the fediverse, the one challenge I still had was image posting and sharing in an *easy* and clean looking way. For images on websites like this, especially from a mobile device, FTP uploading has just been inconvenient and disrupts the focused writing activity.

I had already dabbled a bit in [Pixelfed](https://pixelfed.org), by joining [Pixelfed.Social](https://pixelfed.social) when it was still open for registration. This let me test the functionality for a service similar to Instagram or Imgr, but without ads or tracking. The final leap was setting it up self-hosted so that I could fully own that image data. 
<!--more-->
![PixelFed Image](https://pixelfed.techzerker.com/storage/m/d0931bf747b992a1c83e055753526516f2706111/f47ca2c04ca946119f9a60462ac37f1097982fcc/KazyVryAAjKa1YoQZGvVjQFnUMYmcPuXdqQM61Lm.jpeg)

On my Arch VPS that I host on [Vultr](https://www.vultr.com/?ref=7975115), I had already tried in previous weeks to direct install Pixelfed. Unfortunately, likely because of other self-hosted apps or packages in place, it was a struggle and I just could not get Pixelfed fully operational. In steps Docker, and the [article here](https://jonnev.se/pixelfed-beta-with-docker-and-traefik/) that inspired this updated version with what I had to do differently, as well as more details on the Nginx reverse proxy portion. 

*If you are interested in using Vultr to host a VPS for this or any other self-hosted projects, depending on your project size and plans, you can either get [$10 of VPS Credit](https://www.vultr.com/?ref=7975115) for a small project, or if you're looking for a larger project, you can get [$100 of VPS Credit for 30 days](https://www.vultr.com/?ref=8473015-6G). Both help me out and keep tracking based ads and services off my site.*

## Requirements

- VPS [Operational & Secure](https://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers)
- Docker & Docker-Compose Installed
- Nginx Installed
- [Domain Name](https://www.cloudflare.com/en-ca/products/registrar/) Setup, and Ideally SSL Certificates ready

*Of note, in this install, Nginx is direct installed on the host, as it fit with my existing environment. It is also common to deploy Nginx with Docker as well, which would change these instructions to some extent.*

## Pixelfed Setup

The Pixelfed install will be built directly as a new image, as similar to Jon in the article I referenced, the only pre-built docker images while recent, had no details or notes, so did not inspire the most confidence. Ideally, the steps below should all be run as a non-root user that has *Sudo* capability. 

First, a directory to pull the source to, using /opt/ is a good best practice vs. installing it to a specific users /home directory. 

````
sudo mkdir -p /opt/pixelfed_source
sudo chown $USER:$USER /opt_pixelfed_source
````

Next is pulling down the repository, in my case it's been active development, so I just pulled the most recent, but you can also pull an older *stable* build too if you prefer.

````
git clone https://github.com/pixelfed/pixelfed.git /opt/pixelfed_source
cd /opt/pixelfed_source
git checkout dev
````

This was the first difference I had to work through, where just trying a straight *docker build* was failing, because in the current versions, the *dockerfile* files are not in the root, so the command to build has to be updated to reflect that. I also tagged my build with the pull date, given it's dev so does not have a release number (like v0.10.8) and a commit tag is long and ugly.

````
docker build . -t pixelfed:20200302 -f contrib/docker/Dockerfile.apache
````

There is also a *Dockerfile.fpm* file there for a php-fpm based version, but as of this writing date, I confirmed it is not complete/functioning yet.

Once the docker has built, we can create a directory that the docker compose will run from, which will contain it's settings, as well as the Pixelfed .env settings file.

````
sudo mkdir -p /opt/pixelfed
sudo chown $USER:$USER /opt/pixelfed
cd /opt/pixelfed
````

To copy the example configuration file over:

````
cp ../pixelfed_source/.env.example .env
nano .env
````

Rather then snippets of key settings, I'm removing sensitive parts and posting up my entire .env config file, as the parts I struggled with the most were the ones where documentation is still a work in progress. Like the article I learned from, I stuck with pgsql within Docker.

````
APP_NAME="Pixelfed"
APP_ENV=production
APP_KEY=**blank, we'll generate this further down**
APP_DEBUG=false

APP_URL=https://**your-domain.name**
APP_DOMAIN="**your-domain.name**"
ADMIN_DOMAIN="**your-domain.name**"
SESSION_DOMAIN="**your-domain.name**"
TRUST_PROXIES="*"

LOG_CHANNEL=stack

DB_CONNECTION=pgsql
DB_HOST=db
DB_PORT=5432
DB_DATABASE=pixelfed
DB_USERNAME=pixelfed
DB_PASSWORD=**create a proper secure password!**

BROADCAST_DRIVER=log
CACHE_DRIVER=redis
SESSION_DRIVER=redis
QUEUE_DRIVER=redis

REDIS_SCHEME=tcp
REDIS_HOST=redis
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_DRIVER=smtp
MAIL_HOST=mailtrap.io
MAIL_PORT=587
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=null
MAIL_FROM_NAME="Pixelfed"

OPEN_REGISTRATION=false
ENFORCE_EMAIL_VERIFICATION=false
PF_MAX_USERS=100

MAX_PHOTO_SIZE=64000
MAX_CAPTION_LENGTH=150
MAX_ALBUM_LENGTH=4
MAX_ACCOUNT_SIZE=10000000
IMAGE_QUALITY=100

ACTIVITY_PUB=true
AP_REMOTE_FOLLOW=true
AP_INBOX=true
PF_COSTAR_ENABLED=false

HORIZON_EMBED=true
````

## Docker / Docker-Compose

With that file saved and done, the next step is to setup the docker network and build the `docker-compose` file for the whole process.

````
docker network create pixelfed
docker network create web

touch docker-compose.yml
nano docker-compose.yml
````

Below is my docker compose file, with sensitive details removed and key points highlighted. Of worthy note in my install, I have host mapped the storage in place of an internal docker volume, to a Fuse S3FS mount point for my [S3](https://wasabi.com/cloud-storage-pricing/) storage, which will also make it easier for me to at minimum, backup the raw photo content, and it's cheaper storage than my VPS SSD.

````
version: '3'
services:

  app:
    image: pixelfed:20200302
    restart: unless-stopped
    ports:
      - "8080:80"
    env_file:
      - ./.env
    volumes:
      - "/path/to/s3-storage:/var/www/storage"
      - "app-bootstrap:/var/www/bootstrap"
      - ./.env:/var/www/.env
    networks:
      - web
      - pixelfed

  db:
    image: postgres:9.6.4
    restart: unless-stopped
    networks:
     - pixelfed
    volumes:
     - db-data:/var/lib/postgresql/data
    environment:
     - POSTGRES_PASSWORD=${DB_PASSWORD}
     - POSTGRES_USER=${DB_USERNAME}

  worker:
    image: pixelfed:20200302
    restart: unless-stopped
    env_file:
      - ./.env
    volumes:
      - "/path/to/s3-storage:/var/www/storage"
      - "app-bootstrap:/var/www/bootstrap"
    networks:
      - web  # Required for ActivityPub
      - pixelfed
    command: gosu www-data php artisan horizon

  redis:
    image: redis:5-alpine
    restart: unless-stopped
    volumes:
      - "redis-data:/data"
    networks:
      - pixelfed

volumes:
  redis-data:
  app-bootstrap:
  db-data:

networks:
  pixelfed:
    internal: true
  web:
    external: true
````

If you are already running Nginx with other services on the same server, then you may need to adjust the *8080* Port in: `Ports: -8080:80` to a different available port, and mirror that change when we get to Nginx shortly.

Now we should be able to spin up the image and get it ready for deployment, before we go and sort out Nginx:

````
docker-compose up -d
docker-compose exec app php artisan key:generate

cat .env | grep APP_KEY 
# Make sure there's a value
````

Then the container can be restarted, and some final Pixelfed prep tasks complete to have it ready to go. 

````
docker-compose restart app
docker-compose exec app php artisan config:cache
docker-compose exec app php artisan migrate
# Answer yes
````

At this point, technically, the Pixelfed instance should be up and running, so we'll need to sort out the Nginx portion so we can actually access it and verify that fact!

## Nginx Setup

There are a few different methods I've seen used for Nginx .conf file locations, but they should all work with minor tweaks. In my case, there is a .conf file for each web application in `/etc/nginx/conf.d/nameofapp.conf`

As such, my base `/etc/nginx/nginx.conf` is default install, with just these key settings, everything else being commented out or removed.

````
worker_processes  1;

error_log  logs/error.log;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    include /etc/nginx/conf.d/*.conf;
    default_type  application/octet-stream;

    sendfile        on;

    keepalive_timeout  65;

    gzip  on;
}
````

Then I have the file, `/etc/nginx/conf.d/pixelfed.conf` setup as below, with placing your correct domain name in place, as well as the path to your certificates. 

````
server {
  listen 80;
  server_name **your-domain.name**;
  return 301 https://$server_name$request_uri;
  client_max_body_size 100M;
}
server {
  listen 443 ssl http2;
  server_name **your-domain.name**;
  client_max_body_size 100M;

  ssl_protocols TLSv1.1 TLSv1.2;

  # ** Adjust below to match your certs, mine were from CloudFlare, but there are plenty of guides for LetsEncrypt, etc. if you're not setting up   behind a CDN **

  ssl_certificate /etc/nginx/ssl/**your-domain.name**-tld-cert.pem;
  ssl_certificate_key /etc/nginx/ssl/**your-domain.name**-tld-key.pem;

  access_log /var/log/nginx/**your-domain.name**.log;
  location / {
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_pass http://127.0.0.1:8080;
  }
}
````

Note the *client_max_body_size 100M;* lines, I added these after hours digging through PixelFed and PHP.ini type options because I was able to upload pictures, but only if they were under 1MB, and with a very generic *failure* error. The end solution was that Nginx by default limits file transfers to 1MB if not defined. You can define this here just for Pixelfed, or you can define it globally in the main Nginx.conf file if you prefer. 

With the *pixelfed.conf* file generated and filled, hopefully correctly, you should now be able to restart Nginx so that it picks up the file changes. In the case of Arch (and most modern systems), it's using Systemd, so:

````
sudo systemctl restart nginx
````

If everything at this point has been configured correct, and assuming I have not forgotten any other catch points I ran into at this stage, you should be able to visit *https://your-domain.name*, and get the default Pixelfed home screen. 

## User Creation

If the site loaded for you, and if like myself you're building this primarily for private or small group usage, so turned off Open Registration, you can now use the terminal to create you first user, presumably as an admin:

````
docker-compose exec app php artisan user:create
````

That command above will walk you through several requests to build a user, including asking if the user should be an admin (probably *yes*). Of worthy note, when it asks for **Over-ride manual e-mail verification** (my wording might be off), enter **yes**. This marks the accounts e-mail as already verified, so for a small instance you don't have to focus on e-mail setup right away. 

With the user created, you should be able to login and start using Pixelfed!

## Outstanding Issues

The only issue I currently am still working on resolving, but it may be caused by a caching network appliance that I can't test bypassing right away, is *collections*. 

On my instance when I try to create a collection, it lets me add from recent or by URL posts to a collection, and even lets me *Save* the collection. But roughly 75% of the time when I attempt to *Publish* the collection, I get a generic error message and it does not Publish. When this occurs, my reason I suspect this caching appliance I'm stuck behind, is I can't even delete the half-created collection, until I wait an hour or so. Selecting delete gives me the prompt, but nothing happens even though Nginx logs show is processed and passed the command through correctly. 

A handful of times while trying to test this, a collection created perfect without error and published. Once I've been able to test this on a direct internet line, I'll be able to narrow down if I still have a configuration error, and/or if I have an issue I need to submit back to the Github project, given this is the Dev branch.

I will also add, I have not yet confirmed if *Federation* is working for me, but will be validating that when possible, and updating the article if I have to change any settings.

## Updating Pixelfed

This process I am mostly copying direct from the original article I followed, as this install has been recent enough, there have not been any updates yet for me to validate if this creates problems for me. That being said, the commands all look correct, so I don't anticipate issues. Before updating, especially on *Dev* branch, it's a good practice on your VPS to either take a backup or a snapshot *just in case*. To update to the current Dev:

````
cd /opt/pixelfed_source
git checkout dev
git pull origin dev
git checkout dev
docker build . -t pixelfed:todaysdate -f contrib/docker/Dockerfile.apache
````

Then once that has pulled and built, with the updated today's date to help identify it, it's just two lines to update in the *docker-compose.yml* file:

````
cd /opt/pixelfed
nano docker-compose.yml

app:
    image: pixelfed:todaysdate
  #...
  worker:
    image: pixelfed:todaysdate
````

Then restart the docker-compose, and run the artisan migrate command. Worthy warning if you're new to docker, when we say restart, do **not** run `docker-compose down`, as that deletes this container and wipes the volume data as well, meaning the next *docker-compose up -d* would be like a fresh install (without your images or users), so:

````
docker-compose up -d
docker-compose exec app php artisan migrate
# Answer yes
````

## Wrap Up

At this point, if everything worked (and I know, it often takes several attempts, this took me a while to get all sorted and running!), you should have a fully functioning PixelFed, and can either open registration, or terminal create users for your own small community to post to. 

For updates to the Project, keep an eye on [Pixelfed GitHub](https://github.com/pixelfed/pixelfed), and as they detail on that page, there is a semi-active group on their Riot Matrix.Org channel for help, which I've been over-active in during my install struggles. 

If this article was helpful and you have not yet settled on a VPS provider, you can help me out by trying [Vultr](https://www.vultr.com/?ref=7975115), and we'll keep that whole mess of ads, data tracking, cookies and the like off this corner of the web!
