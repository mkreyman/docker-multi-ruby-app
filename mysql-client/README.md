## Alpine:3.1 based dockerfile for mysql client

To build:

    $ docker build -f ./mysql-client/Dockerfile -t sherbet/mysql-client ./mysql-client

Create a Docker network, i.e. `sherbet-network`, if it hasn't been created:

    $ docker network create sherbet_default

To run and connect to an existing container with the name `mysql-server` on that network:

    $ docker run -it --rm \
        --net=sherbet_default --volumes-from mysql-server \
        sherbet/mysql-client mysql -hmysql-server -uroot

To run shell:

    $ docker run -it --rm mysql-client bash

### Available tools:

* mysql — The MySQL Command-Line Tool
* mysqladmin — Client for Administering a MySQL Server
* mysqlcheck — A Table Maintenance Program
* mysqldump — A Database Backup Program
* mysqlimport — A Data Import Program
* mysqlshow — Display Database, Table, and Column Information
* mysqlslap — Load Emulation Client


    $ docker run --rm mysql-client <TOOL> --version


### Execute SQL statements from an input SQL file:

    $ docker run -v <path to sql>:/sql --link <mysql server container name>:mysql -it mysql-client -h mysql -p <password> -D <database name> -e "source /sql/<your sql file>"



### Quick reference:

http://dev.mysql.com/doc/refman/5.6/en/mysql-command-options.html
http://dev.mysql.com/doc/refman/5.6/en/mysqldump.html

