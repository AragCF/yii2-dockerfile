# Run

```
docker-compose up -d
```

# Prepare to instalation

## Configure network environment

Create alias to host machine

### MacOs

```
sudo ifconfig en0 alias 10.254.254.254 255.255.255.0
```

### Linux

```
sudo ip addr add 10.254.254.254/24 brd + dev eth0 label eth0:1
```

## Configure docker-compose.yml

## Install services

### Install Maria DB

```
  mariadb:
    build: ./provision/mariadb
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: skringo
      MYSQL_USER: skringo
      MYSQL_PASSWORD: skringo
    ports:
      - '3306:3306'
    expose:
      - '3306'
    volumes:
      - './logs/mysql:/var/log/mariadb'
```			

### Install Redis

```			
  redis:
    build: ./provision/redis
    restart: always
    environment:
      - REDIS_VERSION=4.0.9
    ports:
      - '6379:6379'
    expose:
      - '6379'
  redis-commander:
    container_name: redis-commander
    hostname: redis-commander
    image: 'rediscommander/redis-commander:latest'
    build: .
    restart: always
    environment:
      - 'REDIS_HOSTS=local:redis:6379'
    ports:
      - '8081:8081'
```	

### Install Nginx
		
```					
  nginx:
    build: ./provision/nginx
    restart: always
    links:
      - php
    volumes:
      - './:/app'
      - './provision/nginx/etc/conf.d/yii2.advanced.template:/etc/nginx/conf.d/site.template'
      - './logs/nginx:/var/log/nginx'
    environment:
      - NGINX_VERSION=1.13.12-1~stretch
      - NGINX_HOST=yii2-site.loc
      - NGINX_PORT=80
    ports:
      - '80:80'
    command: 'sh -c "envsubst \"`env | awk -F = ''{printf \" $$%s\", $$1}''`\" < /etc/nginx/conf.d/site.template > /etc/nginx/conf.d/default.conf && nginx -g ''daemon off;''"'
```	

#### Set default host

* ./provision/nginx/etc/conf.d/yii2.basic.template
* ./provision/nginx/etc/conf.d/yii2.advanced.template

### Install Elasticsearch

```				
  elasticsearch:
    build: ./provision/elasticsearch
    restart: always
    ports:
      - '9200:9200'
    expose:
      - '9200'
    environment:
      - ELASTICSEARCH_VERSION=5.6.9
      - JAVA_ALPINE_VERSION=8.151.12-r0
      - JAVA_VERSION=8u151
      - LANG=C.UTF-8
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - ES_JAVA_OPTS=-Xms1g -Xmx1g
    ulimits:
      memlock:
        soft: -1
        hard: -1
    mem_limit: 1g
```			

### Install Kibana

```				
  kibana:
    build: ./provision/kibana
    links:
      - elasticsearch
    ports:
      - '5601:5601'
```			

### Install PHP


```					
  php:
    build: ./provision/php/7.1
    restart: always
    links:
      - mariadb
      - redis
      - elasticsearch
    ports:
      - '9001:9000'
      - '8004:8004'
    expose:
      - '9000'
      - '9001'
    environment:
      - PHP_IDE_CONFIG=serverName=skringo.loc
    volumes:
      - './:/app'
      - './provision/php/7.1/xdebug.ini:/etc/php7/conf.d/xdebug.ini'
      - './logs/php7:/var/log/php7'
      - './logs/php7/xdebug:/tmp/xdebug_log'
```

#### Configure php version

* ./provision/php/7.0
* ./provision/php/7.2
* ./provision/php/7.3

#### Set xdebug configuration

```bash
$ docker-compose -f docker-compose.yml -f docker-compose-xdebug.yaml up -d
```

* ./provision/php/7.0/xdebug.ini
* ./provision/php/7.1/xdebug.ini
* ./provision/php/7.2/xdebug.ini

## Create Yii2 application

* cd /app/app && composer create-project --prefer-dist yiisoft/yii2-app-basic ./
* cd /app/app && composer create-project --prefer-dist yiisoft/yii2-advanced-basic ./

