Clone insight-mysql-docker

```sh
git clone git@github.com:christofferartmannmrf/insight-mysql-docker.git
```

In the `insight-mysql-docker` directory, use the start script to start the MySQL server.

```sh
./start-mysql
```

There is now a container running with MySQL. You can see it by running `docker ps`.

The first time we start the server it won't contain any of the data we need to run
Insight. So we will have to import the test data provided by Gutenberg.

To start the MySQL client, run

```sh
docker exec -it insight-mysql /usr/bin/mysql -p
```

It will ask you for the MySQL root password which is `my-secret-pw`.

Once you have opened the MySQL client, run the following commands to import the test data.

```sql
SOURCE /schemas/anubis-create-test-database.sql
USE anubis;
SOURCE /schemas/anubis-test-schema.sql
SOURCE /schemas/anubis-test-data.sql
```

If you try to login into insight now, you will get the following error

```java
Caused by: java.sql.SQLException: Unable to load authentication plugin 'caching_sha2_password'.
```

This is due to a version missmatch between the library Insight uses to connect to MySQL and the
version of MySQL Server we are running. Lucklily there is an easy way to fix this.

In your MySQL client, run

```sql
USE mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'my-secret-pw';
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'my-secret-pw';
```

If you want to stop the MySQL server, run

```sh
docker stop insight-mysql
```
