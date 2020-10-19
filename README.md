# Nuxt.js NGINX Docker
```
cd dockerize-nuxtjs
git clone https://github.com/username/nuxtjs-app
sudo nano docker-compose.yml
```
Change the build: ./nuxtjs-app
```
version: "3"

services:
  nuxt:
    build: ./nuxtjs-app
    container_name: nuxt
    restart: always
    ports:
      - "5000:3000"
    command:
      "npm run start"

  nginx:
    image: nginx:1.17
    container_name: ngx
    ports:
      - "80:80"
    volumes:
      - ./nginx:/etc/nginx/conf.d
    depends_on:
      - nuxt
```
## Getting you instance ready for Docker
Run the following commands to install Docker and Docker Compose
On Ubuntu
```
sudo apt update
sudo apt install -y docker.io git
sudo usermod -a -G docker ubuntu
sudo service docker start
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```
or with Amazon Linux
```
#!/bin/bash
sudo yum -y update
sudo yum install -y docker git
sudo usermod -a -G docker ec2-user
sudo service docker start
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Build and run
Download or clone this repository. Go into the root folder of this repository, and run the following command.
```
docker-compose build && docker-compose up -d
```
## Deployment on server
1. Check the containerID the running nuxtjs-app
```
docker ps
```
2. commit a change to a container
```
docker commit -m "nuxt-js" -a "username" containerID username/nuxtjs-app
```
3. Push the app to dockerhub
```
>>docker push username/nuxtjs-app
```
4. Go to directory deploy-on-server/nuxtapp
```
cd
cd dockerize-nuxtjs/deploy-on-server/nuxtapp
```
5. Go to directory deploy-on-server/nginxproxy
```
cd
cd dockerize-nuxtjs/deploy-on-server/nginxproxy
docker-compose up -d
```
6. Make changes on docker-compose.yml
```
version: "3"

services:
  nuxt:
    image: username/nuxtjs-app
    restart: always
    container_name: nuxt
    ports:
      - "5000:3000"
    # command:
    #   "npm run start"
    environment:
      VIRTUAL_HOST: domain.name
      LETSENCRYPT_HOST: domain.name
      LETSENCRYPT_EMAIL: email
networks:
 default:
  external:
    name: nginxproxy_default
```
7. Deploy Nuxtjs-app
```
docker-compose up -d
```
