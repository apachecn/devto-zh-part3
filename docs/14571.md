# 使用 Masonite 在 Docker 中使用 PostgreSQL

> 原文：<https://dev.to/kentaro0919/working-with-postgresql-with-masonite-7n9>

[docker](https://docs.docker.com/engine/examples/postgresql_service/)
中的文档使用 9.3 版本，但我想尝试一个更新的版本。所以我改成了

Dockerfile

```
FROM ubuntu

RUN apt update
RUN apt install -y postgresql postgresql-contrib gnupg2
RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8

RUN echo "deb http://apt.postgresql.org/pub/repos/apt/ precise-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN apt-get update && apt-get install -y software-properties-common postgresql postgresql-client postgresql-contrib

USER postgres
RUN    /etc/init.d/postgresql start &&\
    psql --command "CREATE USER docker WITH SUPERUSER PASSWORD 'docker';" &&\
    createdb -O docker docker

RUN echo "host all  all    0.0.0.0/0  md5" >> /etc/postgresql/10/main/pg_hba.conf

RUN echo "listen_addresses='*'" >> /etc/postgresql/10/main/postgresql.conf

EXPOSE 5432

VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql"]

CMD ["/usr/lib/postgresql/10/bin/postgres", "-D", "/var/lib/postgresql/10/main", "-c", "config_file=/etc/postgresql/10/main/postgresql.conf"] 
```

构建

```
$ docker build -t eg_postgresql . 
```

运行

```
$ docker run --rm -P --name pg_test eg_postgresql 
```

以
身份运行 PostgreSQL

```
$ docker ps
CONTAINER ID        IMAGE                  COMMAND                CREATED             STATUS              PORTS                                      NAMES
5e24362f27f6        eg_postgresql:latest   /usr/lib/postgresql/   About an hour ago   Up About an hour    0.0.0.0:49153->5432/tcp                    pg_test
$ psql -h localhost -p 49153 -d docker -U docker --password 
```

将 Masonite 中的 env 文件更改为

```
DB_CONNECTION=postgres #NOT PostgreSQL nor postgresql !
DB_HOST=127.0.0.1
DB_PORT=49153
DB_DATABASE=docker
DB_USERNAME=docker
DB_PASSWORD=docker
DB_LOG=True 
```

然后尝试连接到数据库。

```
$craft migrate

It took 4.88ms to execute the query SELECT * FROM information_schema.tables WHERE table_name = 'migrations'
It took 13.64ms to execute the query CREATE TABLE "migrations" ("migration" VARCHAR(255) NOT NULL, "batch" INTEGER NOT NULL)
It took 1.2ms to execute the query SELECT "migration" FROM "migrations"
It took 1.24ms to execute the query SELECT MAX("batch") AS aggregate FROM "migrations"
It took 16.29ms to execute the query CREATE TABLE "users" ("id" SERIAL PRIMARY KEY NOT NULL, "name" VARCHAR(255) NOT NULL, "email" VARCHAR(255) NOT NULL, "password" VARCHAR(255) NOT NULL, "remember_token" VARCHAR(255) NULL, "verified_at" TIMESTAMP(6) WITHOUT TIME ZONE NULL, "created_at" TIMESTAMP(6) WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP(6) NOT NULL, "updated_at" TIMESTAMP(6) WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP(6) NOT NULL)
It took 4.47ms to execute the query ALTER TABLE "users" ADD CONSTRAINT users_email_unique UNIQUE ("email")
It took 1.7ms to execute the query INSERT INTO "migrations" ("migration", "batch") VALUES ('2018_01_09_043202_create_users_table', 1)
Migration table created successfully
[OK] Migrated 2018_01_09_043202_create_users_table 
```

成功了！！