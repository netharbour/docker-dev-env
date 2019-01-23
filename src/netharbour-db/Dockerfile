
# Base image
FROM mysql:5.5

MAINTAINER Craig Tomkow "craig.tomkow@bc.net"

# Timezone
ENV TZ=America/Vancouver

COPY ./docker-entrypoint-initdb.d/ /docker-entrypoint-initdb.d/
COPY ./netharbour.sql /netharbour.sql