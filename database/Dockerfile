FROM mysql:latest

ADD init.sql /docker-entrypoint-initdb.d/init.sql

# RUN sed -i 's/MYSQL_DATABASE/'$MYSQL_DATABASE'/g' /etc/mysql/init.sql
# RUN cp /etc/mysql/data.sql /docker-entrypoint-initdb.d