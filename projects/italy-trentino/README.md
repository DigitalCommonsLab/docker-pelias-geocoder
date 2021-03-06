
# Trentino geocoder
This project is configured to download/prepare/build a complete Pelias installation for Trentino Alto Adige in Italy
Include a importer for Open Data of Trentino Alto Adige:
https://github.com/DigitalCommonsLab/pelias-importer-trentino-opendata

used as service in the [docker-compose.yml](https://github.com/DigitalCommonsLab/docker-pelias-geocoder/blob/master/projects/italy-trentino/docker-compose.yml#L143)

## Setup
Please refer to the instructions at https://github.com/pelias/docker in order to install and configure your docker environment.

The file *.env* contains custom ENV variable passed to docker compose to specify
data working directory and docker connection parameters:
```
COMPOSE_PROJECT_NAME=pelias
DOCKER_USER=1000
DOCKER_HOST=unix:///var/run/docker.sock
DATA_DIR=/data
PELIAS_CONFIG=/var/www/pelias/docker/projects/italy-trentino/pelias.json
```
*PELIAS_CONFIG* is absolute path di locate pelias config file

## Run a Build
To run a complete build configure and execute the script *build.sh*
or the following commands separately:

first time setup or reset database
```bash
pelias compose pull
pelias elastic start
pelias elastic wait
#pelias elastic drop #if you want reset data
pelias elastic create
```

### Download data, firstime or update
```bash
pelias download wof
pelias download osm
pelias download transit
pelias download trentino
```

don't run ```pelias download all``` because return error in openaddress download

because of this:
https://github.com/pelias/openstreetmap/issues/542
at this step run:
```bash
cd /data/openstreetmap/
mv trentino-alto-adige.pbf trentino-alto-adige.osm.pbf
```

### Import data
only for updates data run this before: pelias elastic drop

```bash
pelias prepare trentino
#
pelias prepare polylines
pelias prepare interpolation
pelias prepare placeholder
```

import new prepared data into elastic index
```bash
#pelias import wof cause error
pelias import osm
#pelias import transit invalid quote
pelias import oa
pelias import polylines
```

### First time
```bash
pelias compose up
```

and for testing UI

```bash
docker-compose up -d trentino-opendata-test
```

### API CONFIG

defaults values:
https://github.com/pelias/config/blob/master/config/defaults.json
