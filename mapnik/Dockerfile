FROM mguentner/renderd-osm
MAINTAINER Patrice Cavezzan <pcavezzan@gmail.com>

# Just override renderd run script to add password for postgres
COPY ./renderd/run /etc/service/renderd/run
RUN chown root:root /etc/service/renderd/run 
RUN chmod u+x       /etc/service/renderd/run 
