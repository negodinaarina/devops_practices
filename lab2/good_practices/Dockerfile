FROM postgis/postgis:17-3.4

COPY ["init.sql", "/docker-entrypoint-initdb.d/"]

RUN adduser --system not_root

USER root

RUN chown -R not_root /var/lib/postgresql/data /var/run/postgresql

USER not_root