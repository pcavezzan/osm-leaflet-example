# How to quickly create your own map

This project is just a start to quickly demonstrate how easy it is to create a map with our own geodata based on:
* PostgreSQL with PostGis extension to store our geodata
* Mapnik to render tiles from the geodata
* Apache to host our simple html client file to render a simple Map using LeafletJS

## Start the demo

All our GIS is containerized. So basically, you only need to run only commandline:
```
$>cd <gis_dir_depo_cloned>
$>rm data/.gitignore
$>docker-compose up --build
```
Afer a few seconds (when all containers are up), just go to http://localhost and you should see your own street map !

**_If you have not imported yet your data, just import your data then restart mapnik container as it would probably not have been able to connect to the geodatabase._**

## In details

### GeoData with PostGis

First of all, we need to import our OSM (OpenStreetData) data. For that, we are going to do two things:
1. install osmosis to convert uncompress osm data into an osm file
```
$>sudo apt-get install osmosis
$>osmosis --read-xml haute-normandie-latest.osm.bz2 --write-xml /tmp/haute-normandie.osm
```
2. install osm2pgsql to import osm data into postgresql (or just use a docker image to do that :smiley:)
```
$>docker run -i -t --rm -v /tmp:/osm openfirmware/osm2pgsql -c 'osm2pgsql -m -d gis -H <docker_ip_postgis_container> -U gis -W /osm/haute-normandie.osm'
```
Then you should see your table in GIS database:
```
$>psql -h <ip_docker_container_postgis> -U gis gis
$>\dt
```
Now your geodatabase is ready, let''s use it.

### Rendering your tiles

To render your data, we use MapNik that is going to render our tiles from the geodatabase. In this example, we''ve just customized the really nice container [renderd-osm](https://github.com/mguentner/docker-renderd-osm) by adding the password to connect to our geodatabase in renderd script.

### Get our map

Finally we need a map to interract with our tiles (zoom in, zoom out basic abilities). For that, we use [LeafletJS](http://leafletjs.com/).
The javascript file is served by an apache server that communicate throw mapnik to render our geo data.
