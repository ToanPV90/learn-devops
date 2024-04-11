# ReadMe_static

## Mac

[hub.docker.com » starting mysql locally](https://hub.docker.com/_/mysql)

Start the MySQL database locally with the following command:

```bash
# Start the MySQL database locally
docker run --rm -it --name mysql8 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=secret -d mysql:8
# Execute a bash shell in the container
docker exec -it mysql8 bash
```

```bash
# Connect to the MySQL database in the container
mysql -hlocalhost -uroot -psecret
```
