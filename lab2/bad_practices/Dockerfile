FROM postgis:latest

ENV POSTGERS_USER=arina
ENV POSTGRES_PASSWORD=password
ENV POSTGRES_DATABASE=docker

ADD ["init.sql", "/docker-entrypoint-initdb.d/"]