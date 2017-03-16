## Database data storage for mysql-server container.

Container built from this image serves as a persistent data storage for mysql-server, and, when present, would be mounted as `/var/lib/mysql` on the mysql-server container.

### To build, apart from the rest of the sherbet application containers:

    $ docker-compose build dbstore

...otherwise, it would be built when you bring docker compose up:

    $ docker-compose up

...or just the database container:

    $ docker-compose up db

Then the database can be accessed with your favorite db tool on port 6306. The port 3306 on the container has been mapped to port 6306 on the host (configurable in `*-compose.yml`), so you could access the container directly and avoid port conflicts with your local mysqld installation, if it exists, which usually runs on port 3306. Note, containers that talk to the mysql-server container still access it over port 3306.


#### Note: `docker-compose down` not only stops all containers but also destroys them. If you want to keep your changes around, use `docker-compose stop` instead, or export your work before shutting down docker compose.
