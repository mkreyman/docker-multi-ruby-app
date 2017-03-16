FROM mysql/mysql-server:5.6
MAINTAINER Mark Kreyman <mark@kreyman.com>

ADD config/custom.cnf /etc/my.cnf
RUN mkdir /schemas

# Uncomment if there are any schema files you'd like to import
# ADD schemas/*.sql /schemas/

VOLUME /var/lib/mysql
VOLUME /schemas

EXPOSE 3306

CMD ["mysqld"]
