FROM postgres:10.0-alpine

# Set Workdir
WORKDIR /app

COPY postgresql.conf.tpl /etc/postgresql/postgresql.conf

CMD ["-c", "config_file=/etc/postgresql/postgresql.conf"]