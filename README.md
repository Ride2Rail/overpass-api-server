# Overpass API server

In this document can be found the characteristics of the server that runs the different dockers containing the countries and their Overpass APIs, all the steps followed for the current installation and instructions in case that the countries list is expanded.

The system is based on the [`wiktorn/Overpass-API`](https://github.com/wiktorn/Overpass-API) container image.

## Server characteristics:

Disk size: 500GB
Cores: 1
RAM: 4GB

## Installation

The first installation includes the following countries:

| Country        | Size (GB) |
| -------------- | --------- |
| Belgium        |         4 |
| Czech Republic |         6 |
| Finland        |         6 |
| France         |        19 |
| Greece         |         4 |
| Italy          |        17 |
| Norway         |         6 |
| Portugal       |         4 |
| Slovakia       |         3 |
| Spain          |        11 |
| Switzerland    |         5 |



### Install Docker:

All the info relative to installing dockers, can be found in the following link: [Link to docker installation guide](https://docs.docker.com/install/linux/docker-ce/ubuntu/)

### Folders creation:

The following folders are the ones to be mapped on the Docker container creation:

```shell
sudo mkdir -p /var/ride2rail/logs/nginx-entrypoint
sudo mkdir -p /etc/ride2rail/nginx/nginx-entrypoint/sites-available
sudo mkdir -p /etc/ride2rail/nginx/nginx-entrypoint/ssl # folder for the SSL certificate in case to use one in the future

sudo mkdir -p /var/ride2rail/overpass_api/belgium/db
sudo mkdir -p /var/ride2rail/overpass_api/czech-republic/db
sudo mkdir -p /var/ride2rail/overpass_api/finland/db
sudo mkdir -p /var/ride2rail/overpass_api/france/db
sudo mkdir -p /var/ride2rail/overpass_api/greece/db
sudo mkdir -p /var/ride2rail/overpass_api/italy/db
sudo mkdir -p /var/ride2rail/overpass_api/norway/db
sudo mkdir -p /var/ride2rail/overpass_api/portugal/db
sudo mkdir -p /var/ride2rail/overpass_api/slovakia/db
sudo mkdir -p /var/ride2rail/overpass_api/spain/db
sudo mkdir -p /var/ride2rail/overpass_api/switzerland/db
```

Once all the folders have been created, we change its owner and permissions

```shell
sudo chown -R 999:999 /var/ride2rail/
sudo chmod -R g+rws /var/ride2rail/
```

### Nginx Configuration files:

Once the different folders have been created, place the nginx.conf and ride2rail-entrypoint.conf files inside the folders:

```shell
sudo cp <path-to-file>/nginx.conf /etc/ride2rail/nginx/nginx.conf
sudo cp <path-to-file>/ride2rail-entrypoint.conf /etc/ride2rail/nginx/nginx-entrypoint/sites-available/ride2rail-entrypoint.conf
```

This conf files are the ones mapped inside the nginx docker container.

### Docker creation:

The following steps are related to the creation of the Docker network and all the containers:

```shell
## Network creation
docker network create --subnet=172.18.0.0/16 ride2railnet

## Pulling docker images
docker pull nginx
docker build -f Dockerfile_nginx -t eurecat-uds/nginx . # To run this line, make sure to be inside Dockerfile_nginx folder

docker pull wiktorn/overpass-api

## Container creation
# Belgium
docker run --net ride2railnet --ip 172.18.0.150 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/belgium-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/belgium/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/belgium/db:/db \
    -p 8080:80 \
    -i -t \
    --name overpass_belgium wiktorn/overpass-api

# Czech Republic
docker run --net ride2railnet --ip 172.18.0.151 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/czech-republic-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/czech-republic/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/czech-republic/db:/db \
    -p 8081:80 \
    -i -t \
    --name overpass_czech-republic wiktorn/overpass-api

# Finland
docker run --net ride2railnet --ip 172.18.0.152 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/finland-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/finland/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/finland/db:/db \
    -p 8082:80 \
    -i -t \
    --name overpass_finland wiktorn/overpass-api

# France
docker run --net ride2railnet --ip 172.18.0.153 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/france-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/france/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/france/db:/db \
    -p 8083:80 \
    -i -t \
    --name overpass_france wiktorn/overpass-api

# Greece
docker run --net ride2railnet --ip 172.18.0.154 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/greece-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/greece/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/greece/db:/db \
    -p 8084:80 \
    -i -t \
    --name overpass_greece wiktorn/overpass-api

# Italy
docker run --net ride2railnet --ip 172.18.0.155 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/italy-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/italy/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/italy/db:/db \
    -p 8085:80 \
    -i -t \
    --name overpass_italy wiktorn/overpass-api

# Norway
docker run --net ride2railnet --ip 172.18.0.156 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/norway-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/norway/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/norway/db:/db \
    -p 8086:80 \
    -i -t \
    --name overpass_norway wiktorn/overpass-api

# Portugal
docker run --net ride2railnet --ip 172.18.0.157 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/portugal-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/portugal/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/portugal/db:/db \
    -p 8087:80 \
    -i -t \
    --name overpass_portugal wiktorn/overpass-api

# Slovakia
docker run --net ride2railnet --ip 172.18.0.158 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/slovakia-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/slovakia/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/slovakia/db:/db \
    -p 8088:80 \
    -i -t \
    --name overpass_slovakia wiktorn/overpass-api

# Spain
docker run --net ride2railnet --ip 172.18.0.159 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/spain-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/spain/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/spain/db:/db \
    -p 8089:80 \
    -i -t \
    --name overpass_spain wiktorn/overpass-api

# Switzerland
docker run --net ride2railnet --ip 172.18.0.160 \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/switzerland-latest.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/switzerland/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/switzerland/db:/db \
    -p 8090:80 \
    -i -t \
    --name overpass_switzerland wiktorn/overpass-api


# Nginx Entrypoint
docker run --net ride2railnet --ip 172.18.0.100 \
    --name nginx-entrypoint \
    -v /var/ride2rail/logs/nginx-entrypoint:/var/log/nginx \
    -v /etc/ride2rail/nginx/nginx.conf:/etc/nginx/nginx.conf:ro \
    -v /etc/ride2rail/nginx/nginx-entrypoint/sites-available:/etc/nginx/sites-available:ro \
    -v /etc/ride2rail/nginx/nginx-entrypoint/sites-available/ride2rail-entrypoint.conf:/etc/nginx/sites-enabled/ride2rail-entrypoint.conf:ro \
    -v /etc/ride2rail/nginx/nginx-entrypoint/ssl:/etc/nginx/ssl:ro \
    -d -p 443:443 -p 80:80 eurecat-uds/nginx

```

### Start Dockers

To run all the dockers, first start all counties:

```shell
docker start overpass_belgium
docker start overpass_czech-republic
docker start overpass_finland
docker start overpass_france
docker start overpass_greece
docker start overpass_italy
docker start overpass_norway
docker start overpass_portugal
docker start overpass_slovakia
docker start overpass_spain
docker start overpass_switzerland
```

Then start the nginx-entrypoint:

```shell
docker start nginx-entrypoint
```

## Adding Countries

To add countries into the server, first of all you should create the folder for it's database:

```shell
sudo mkdir -p /var/ride2rail/overpass_api/<country>/db
```

Then, open a new screen in order to be able to close safely the connection without cutting the download process:

```shell
screen -R # this opens a new screen

#inside a screen use key combination:
ctrl+a , d # detach the screen, you can close connection to server safely
```

Once inside a screen, create the new docker container:

```shell
docker run --net ride2railnet --ip 172.18.0.xxx \
    -e OVERPASS_META=yes \
    -e OVERPASS_MODE=init \
    -e OVERPASS_PLANET_URL=http://download.geofabrik.de/europe/<country>.osm.bz2 \
    -e OVERPASS_DIFF_URL=https://download.openstreetmap.fr/replication/europe/<country>/minute/ \
    -e OVERPASS_RULES_LOAD=10 \
    -v /var/ride2rail/overpass_api/<country>/db:/db \
    -p 8090:80 \
    -i -t \
    --name overpass_<country> wiktorn/overpass-api
```

Notice that that process can last for hours, that's why you should detach screen connection with 'ctrl+a , d' and exit the server.

You can check the process of the container creation by accessing again the screen (screen -R).

Stop nginx-entrypoint container and add the new endpoint to '/etc/ride2rail/nginx/nginx-entrypoint/sites-available/ride2rail-entrypoint.conf' with a text editor like vim

```shell
location /<country> {
	rewrite /<country>/(.*) /$1  break;
	proxy_pass         http://overpass_<country>:80;
	proxy_redirect     off;
}
```

Once this lines are added, you can start the docker with the new overpassAPI and the entrypoint:

```shell
docker start overpass_<country>
docker start nginx-entrypoint
```



