# How to quickly create your own map

Pretty recently, I have to deal with problem for one client. For one of his customer, he had to build a GIS (Geographic Information System). 

The only problem we faced pretty quickly was that, for some reasons, we could not use unfortunatly google maps. So we decided to do, like big web companies such as Flickr or FourSquare, to build our GIS on OpenStreetMap. 

This project is just a start to quickly demonstrate how easy it is to create a map with our own geodata based on:
* PostgreSQL with PostGis extension to store our geodata
* Mapnik to render tiles from the geodata
* Apache to host our simple html client file to render a simple Map using LeafletJS

## Start the demo

All our GIS is containerized. So basically, you only need to run only commandline:
```
$>cd <gis_dir_depo_cloned>
$>docker-compose up --build
```
Afer a few seconds (when all containers are up), just go to http://localhost and you should see your own street map !

## In details

### GeoData with PostGis

First of all, we need to import our OSM (OpenStreetData) data. For that, just install OSM tools such as osmosis, osm2pgsql (under Ubuntu, `$>sudo apt-get install osmosis osm2pgsql`).
```
$>osmosis --read-xml haute-normandie-latest.osm.bz2 --write-xml haute-normandie.osm
$>osm2pgsql -m -d gis -H <ip_docker_container_postgis> -U gis -W haute-normandie.osm
```
Then you should see your table in GIS database:
```
$>psql -H <ip_docker_container_postgis> -U gis gis
$>\dt
```
Now your geodatabase is ready, let''s use it.

### Rendering your tiles

To render your data, we use MapNik that is going to render our tiles from the geodatabase. In this example, we''ve just customized the really nice container [renderd-osm](https://github.com/mguentner/docker-renderd-osm) by adding the password to connect to our geodatabase in renderd script.

### Get our map

Finally we need a map to interract with our tiles (zoom in, zoom out basic abilities). For that, we use [LeafletJS](http://leafletjs.com/).
The javascript file is served by an apache server that communicate throw mapnik to render our geo data.