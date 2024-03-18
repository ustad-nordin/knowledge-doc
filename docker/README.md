# Docker

## Introduction

Docker is a way to run an app in a container. The big advantage is, that you can copy the container to another machine and run it there, with almost no modification. The other machine doesn't need to install libraries and stuff, just run the container and it works.

So an app runs in a container. From the app's context the container is an OS and has things like network or filesystem access. However, a container is a sandbox system, meaning, you can't get real network or filesystem access of the hostmachine. To get network access of the host, you have to expose the container's port to the outside world (which is the OS) and than map the exposed container port to the realworld port.



## Getting started

To create a container, do the following steps:

1. We need to create a **Dockerfile**

```
touch Dockerfile
```

#### FROM

Next we need to define what image we want and what version:

```
# we want image of node version 14 LTS (Long Term Support)
FROM node:14
```
So what happens here, FROM inherits an existing image. So in this case, Node.js version 14 image will be copied, that contains all the necessary files to run node.

#### WORKDIR
WORKDIR is the working directory/folder from where the commands like **RUN**, **CMD**, **ADD**, **COPY** and **ENTRYPOINT** will run. If you don't provide one, Docker will create one automatically.

So to define a WORKDIR, do like this:

```
WORKDIR /app		# or /project or /whatever
```

For more in-depth explanation go to this [docker doc](https://twg.io/blog/things-i-wish-i-knew-about-docker-before-i-started-using-it/).

#### COPY
Before we can run `npm install` to install packages defined in package.json, we need to copy the package files to the working directory `/app`.

```
COPY package*.json ./
```

#### RUN
Now we can run the command to install the node.js packages in the working directory 🤩 (remember, we are running from the working directory defined in WORKDIR).

```
RUN npm install
```

[Here](https://www.docker.com/blog/getting-started-with-docker-using-node-jspart-i/) a nice article about running and building docker images.

#### .dockerignore
Before we copy the rest of the sourcecode files to the working directory, we need to ignore **node_modules** folder. That's because the node_modules folder is already created by the command `RUN npm install` in the previous step.

So we create a `.dockerignore` file and add the following:

```
node_modules
```
That's basically it.

Now we need to copy the rest of the files:

```
COPY . .
``` 
This will copy all the files of our node.js application.

#### ENV
Here we can define our environment variables. So we can set our passwords and some other sensitive data, that can be read by the application. For example, in node.js this will be `process.env.PORT`.
So to add PORT variable:

```
ENV PORT=8080
```

#### EXPOSE
The problem with setting a tcp port, is that it can't be accessed from outside. So we have to tell Docker to expose the desired tcp port, like this:

```
EXPOSE 8080
```

#### CMD
Now we have to execute a command to run the application, for node.js we can do as follows:

```
RUN [ "npm", "run", "start" ]
```
So, run command is actually an array that forms one command.


#### BUILD IMAGE
Now we finished our **Dockerfile**, we can build the image in the terminal:

```
sudo docker build -t nordin/node_demo .
```
So what this does, is building an image from the current folder and name it `nordin/node_demo`.

#### Docker Run
To run the built image, execute:

```
# last paramater is the built image file, which looks like a hashed value
sudo docker run -p 5000:8080 b9ab575fdcd8
```
`-p 5000:8080` means publish port 5000 to the internal image port 8080. So port 5000 from outside is mapped to the node.js app's port 8080, which was defined in **Dockerfile** with the EXPOSE key. This means we can access the app like: `http://domain:5000/`


For more descriptive information go to [Official's Docker site](https://www.docker.com/blog/getting-started-with-docker-using-node-jspart-i/).

#### List docker images
To show a list of built images:

```
sudo docker images
```
The result looks like below:

REPOSITORY      | TAG    | IMAGE ID     | CREATED        | SIZE
----------------|--------|--------------|----------------|-------
nordin/demo_app | latest | b9ab575fdcd8 | 46 minutes ago | 947MB
node            | 14     | b90fa0d7cbd1 | 2 weeks ago    | 943MB
hello-world     | latest | bf756fb1ae65 | 10 months ago  | 13.3kB




## Docker Compose
Docker Compose is for orchestrating multiple containers. Imagine we have containers like NginX, MongoDB, Fileserver, Backend etc. They all form one big application. With Docker Compose we can start all those containers in certain order and do other management stuff.




## Docker NginX Setup
To install NginX we go to the [Docker Hub](https://hub.docker.com) and search for `nginx`.

Than there's a command you can copy: `docker pull nginx`.


### Reverse Proxy Server
This is a system where traffic from one point goes to multiple points. For example:
![](https://blog.jscrambler.com/content/images/2018/11/jscrambler-blog-scaling-node-js-socket-server-redis-nginx-schematics.png)

As you see in the picture above, **nginx**, the blue circle, is a **reversed proxy server**. The green cicles are the node.js services, each running in its own container.


## Docker MongoDB Setup


# Docker Compose

## Introduction

Met docker-compose kun je meerdere services op een gecoordineerde manier opstarten. Vaak heb je een applicatie wat bestaat uit een frontend, backend, database enz. Al deze services kun je met 1 configuratie file creeeren, namelijk met docker-compose.yml.

## Mijn SEMIRA app configuratie

```yml
version: "3.7"

# my service apps
services:

    #Reverse Proxy server
    #This serves semira-app, semira-api
    ###
    reverse_proxy_semira:
        container_name: revproxsemira
        hostname: revproxsemhost
        image: nginx # het is wel handig om de volgende keer een versie mee te geven
        restart: unless-stopped
        ports:
            - 80:80
            - 443:443
        volumes:
            - ./reverseproxy/config/nginx:/etc/nginx:ro
            - ./certbot/config:/etc/letsencrypt:ro
            - ./certbot/www:/var/www/certbot:ro
            - /var/run/docker.sock:/tmp/docker.sock:ro
        networks:
            - semira-network
        depends_on:
            - semira-api
            - semira-app

    #Semira webapp
    ###
    semira-api: # onze pocketbase backend
        container_name: semira-backend
        image: 53654f689fb3 # zelf gemaakte image dmv docker en Dockerfile
        restart: unless-stopped
        expose:
            - "8090" # exposes 8090 within semira-network only
        volumes: # folder vd host wordt gemapt met folder in de container
            - ../SEMIRA/pocketbase/pb_data:/pb_data
        networks:
            - semira-network

    #Semira backend api
    ###
    semira-app: # webapp geserveerd door nginx
        container_name: semira-frontend
        image: nginx #nginx image dat van de docker-hub wordt gedownload
        restart: unless-stopped
        expose:
            - "80" # publisht port binnen docker netwerk genaamd semira-network
        volumes: # folder in de container wordt gemapt met lokale folder (dus in de host)
            - ../SEMIRA/semira-app/src:/usr/share/nginx/html
        networks:
            - semira-network

    portainer:
        image: portainer/portainer-ce:latest
        ports:
            - 9443:9443
        volumes:
            - ../PORTAINER/portainer_data:/data
            - /var/run/docker.sock:/var/run/docker.sock
        restart: unless-stopped
        networks:
            - semira-network

volumes:
  portainer_data:

# private docker network, so containers using this network can communicate
# with each other.
networks:
    semira-network:
            #driver: bridge
```

We hebben hierboven een aantal services, nl.:

- reverse proxy server
- semira-app (frontend)
- semira-api (backend)
- portainer (docker monitoring tool)

## SSL met Reverse proxy server

Verbindingen die komen van buiten naar de RPS (Reverse Proxy Server) worden versleuteld met SSL. We maken gebruik van de **Let's Encrypt** certificaten.

Om de certificaten aan te maken gebruiken we de tool **certbot**.
We gebruiken de volgende commando om een certificaat voor een domein aan te maken:

```
sudo certbot certonly --logs-dir ./certbot/log --config-dir ./certbot/config
```

Hiermee heb ik certificaten aangemaakt voor mijn domeinen semira-app.duckdns.org en semirabackend.duckdns.org.

Je krijgt een aantal vragen die je moet beantwoorden, zoals je mail-adres en domeinnaam. De certificaten wil ik in de folder ./certbot/config in mijn /home folder zetten. Daarmee heb ik niet zo gauw last van root permissies en kan ik dit folder eenvoudig mappen met folders in de containers.

## NGINX reverse proxy server

Om NGINX als RPS te configureren kan het best wel tricky zijn. Op internet zie ik een hoop verschillende configuraties wat nogal verwarrend was. Maar hieronder vind je een werkend configuratie:

```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}

    http {

    include       /etc/nginx/mime.types;
    resolver 127.0.0.11;

    server {

        listen 80;
        server_name semirabackend.duckdns.org;

        location ~ /.well-known/acme-challenge/ {
            root /var/www/certbot;
        }

        location / {
            return 301 https://$server_name$request_uri;
        }
    }

    server {
        listen 80;
        server_name semira-app.duckdns.org;

        location ~ /.well-known/acme-challenge/ {
            root /var/www/certbot;
            #root /etc/nginx/certs;
        }

        location / {
            return 301 https://$server_name$request_uri;
        }
    }


    server {
        listen 443 ssl;
        server_name semirabackend.duckdns.org;

        ssl_certificate /etc/letsencrypt/live/semirabackend.duckdns.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/semirabackend.duckdns.org/privkey.pem;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://semira-api:8090;
        }
    }

    server {
        listen 443 ssl;
        server_name semira-app.duckdns.org;

        ssl_certificate /etc/letsencrypt/live/semira-app.duckdns.org/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/semira-app.duckdns.org/privkey.pem;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://semira-app;
        }
    }


     server {
        listen 443;
        server_name xmonitor.duckdns.org;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass https://portainer:9443;
        }
    }
}
```

This RPS proxies 3 domain names:

- semirabackend.duckdns.org (--> semira-api)
- semira-app.duckdns.org (--> semira-app)
- xmonitor.duckdns.org (--> portainer)

Portainer container handles ssl by itself, so no need to create a ssl certificate.

Here are some website links to good resources about docker-compose and RPS:

- [HTTPS using Nginx and Let's encrypt in Docker, The best solution!](https://mindsers.blog/post/https-using-nginx-certbot-docker/)
- [Setup SSL with Docker, NGINX and Lets Encrypt](https://www.programonaut.com/setup-ssl-with-docker-nginx-and-lets-encrypt/)
- [How to setup NGINX reverse proxy with automatic Lets Encrypt SSL Certificate ](https://alexgallacher.com/how-to-setup-nginx-ssl-on-docker/)
- [An Elegant way to use docker-compose to obtain and renew a Let’s Encrypt SSL certificate](https://blog.jarrousse.org/2022/04/09/an-elegant-way-to-use-docker-compose-to-obtain-and-renew-a-lets-encrypt-ssl-certificate-with-certbot-and-configure-the-nginx-service-to-use-it/)
- [Setting up a Reverse-Proxy with Nginx and docker-compose](https://domysee.com/blogposts/reverse-proxy-nginx-docker-compose)

