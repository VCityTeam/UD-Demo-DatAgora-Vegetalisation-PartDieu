## What this demo is about
A demo illustrating how to:
  - serve an [UD-Viz based](https://github.com/VCityTeam/UD-Viz) urban scene, that includes [SpatialMultimediaDB content display](https://github.com/VCityTeam/UD-Serv/tree/master/API_Enhanced_City)
  - uploading several urban data files to 
    [geoserver](https://github.com/kartoza/docker-geoserver)
  - serving the corresponding urban data layers (with the same geoserver) for
    display in the urban scene  
  - additional display of building sets based on the 3DTiles format

This demo was developed for the 
[place of vegetation in cities project](https://github.com/VCityTeam/DatAgora/wiki/Vegetalization-Project)
within DatAgora.

## Docker containers used by this demo
This demo assumes the existence of the following docker containers:
- A [Geoserver docker](https://hub.docker.com/r/kartoza/geoserver/)
- A [Geoserver setup docker](https://github.com/VCityTeam/Geoserver-Setup-docker)
- A [3DTiles server docker](https://github.com/VCityTeam/3DTiles-Server-docker)
- A [Postgres docker](https://hub.docker.com/_/postgres)
- A [Spatial Multimedia DB docker](https://github.com/VCityTeam/Spatial-Multimedia-DB-docker)
- A [UD-Viz docker](https://github.com/VCityTeam/UD-Viz) located in this folder


## Running the demo for the first time
The only pre-requisite is to have a host with 
[docker compose](https://docs.docker.com/compose/) installed. Once docker is installed, start the docker daemon.

If you wish to modify the default ports (as well as other parameters) used by this demo then you might consider editing the [`.env`](.env) docker-compose environment file.

In order to launch the demo (from a terminal) clone this repository and
change the directory to be the one holding this Readme.md file and run the
following command (you can add "-d" at the end of this line to run the command in background):
```
docker-compose up
```

The setup building of images might take up to several minutes. Once the geoserver-setup docker exited, all the components should be up and running.
From this moment, you can:
 - access the web-gl based demo by browsing the `http://localhost:8999` URL
 - optionally access to the geoserver web interface by browsing the 
  `http://localhost:8998/geoserver` URL

By default, this demo uses ports between 8995 and 8999 included. These ports are configurable by editing the 
[docker-compose .env file](https://docs.docker.com/compose/env-file/)
located in the [same directory](.env). Refer to the comments within that file
for a documentation of the roles of the respective variables.

## Updating the demo

To ensure the update goes smoothly, you will need to run several commands to make sure docker won't use previously built images, thus preventing certain modules from updating.
(All the commands used in the tutorial below are documented [here](https://docs.docker.com/reference/))
First, you will need to stop the current demo. This can be done by going into the directory where this ReadMe is located, and run the following command :
```
docker-compose down
```
This will stop all the containers launched when you last ran the demo.

Next step is removing the outdated images. If you know which parts of the demo were updated precisely, you can use
```
docker images
```
to get the images id and then 
```
docker rmi <Image ID>
```
to remove each of the images. The names of the images built by this demo are written in the docker-compose.yml file


If you don't know exactly what to update,running the following command will remove all the images built by this demo that could have been updated, based on their name :
```
docker rmi -f $(sudo docker images "datagora*" -q)
```
If you have other datagora dockers with images prefixed with "datagora", you should refrain from using this method and remove the images one by one.

Once the images are removed, you will need to rebuild the demo. To ensure docker won't use cached data that may take priority over the new updated data, run the following command :
```
docker build --no-cache
```
Finally, when the build is completed, you can once again run (with "-d" if you need it in background.) :
```
docker compose up
```

## Setup

To setup this demo you can use the .env file available to configure several parameters for each of the six dockers used. To configure the UD-Viz view, you may need to configure the extent and camera settings directly in the config file. These parameters should be the same for each install of the demo and you won't need to change them for a standard install, but they can easily be modified if you need it.

If you need to reinstall the demo with different parameters, be sure to fully remove all the previous containers, images and volumes used by the previous demo to ensure no information remains cached.

## Known issues

### Dockers not awaiting each other

Some dockers need to wait for another docker to be up and running so they can complete their task properly. In normal circumstances they do so. However, it has been observed on occasion that the SpatialMultimediaDB will start running while the postgres docker has not finished its setup, resulting in the SpatialMultimediaDB being unable to connect to the database.
 If a message similar to the one shown below shows up, relaunching the demo should solve the issue. 
   ```
   extended_doc_api | /api
   extended_doc_api | Trying to connect to Database...
   extended_doc_api | Config :  postgresql://postgres:password@postgres:5432/extendedDoc
   extended_doc_api | Connection failed (psycopg2.OperationalError) could not connect to server: Connection refused
   extended_doc_api |      Is the server running on host "postgres" (172.22.0.2) and accepting
   extended_doc_api |      TCP/IP connections on port 5432?
   extended_doc_api | 
   extended_doc_api | (Background on this error at: http://sqlalche.me/e/e3q8)
   ```
If it doesn't, check that your .env file is properly configured.

### Geoserver crashing when refreshing UD-Viz

We encountered an issue where reloading the UD-Viz webpage caused the geoserver to crash. This happened on a MAC while using Chrome browser.
It is not yet clear what caused this issue, but relaunching the demo ended up solving it.
