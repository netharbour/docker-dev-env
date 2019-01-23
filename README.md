# docker-dev-env
A docker based development environment for Netharbour

### Requirements

* Docker 18.06.0+
* Docker Compose 1.22.0+
  * compose file format 3.7+

### Install
1. `git clone https://github.com/netharbour/docker-dev-env.git`
2. `git clone https://github.com/netharbour/netharbour.git docker-dev-env/src/netharbour-app/netharbour`

### (Optional) Configuration
* Edit netharbour configuration files that are bind mounted into the netharbour codebase (`docker-dev-env/src/netharbour-app/netharbour-conf`)
* Edit system configuration files that are copied into the container (`docker-dev-env/src/netharbour-app/system-conf`)

### Run
* `cd docker-dev-env`
* `docker-compose up -d`
* `docker-compose down`

###### Check logs
* `docker logs -f netharbour-app`
* `docker logs -f netharbour-db`

###### Connect to container
* `docker exec -it netharbour-app /bin/bash`
* `docker exec -it netharbour-db /bin/bash`

###### Manage processes _inside_ netharbour-app container
* `supervisorctl status`
* `supervisorctl restart httpd`

### Development Notes
* The netharbour/ codebase directory is bind mounted to /var/www/html/. No rebuilding of the netharbour image is needed between code changes.
* The database container will always mount with ip 172.20.0.2. Useful for reliably connecting an IDE to the database.
* The RRD files live in a docker volume which is mounted to /opt/netharbour/rrd-files in the netharbour-app container.
* The netharbour-app container uses host networking, so ensure your development machine ran reach your test network devices for polling.
