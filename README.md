![Cassandra Logo](cassandra-logo.png)

# Cassandra Practical Works - template
A 101 repository containing the template for practical works to use Apache Cassandra on different languages.

This repository was created to help students using Cassandra at IUT Limoges, by William Pinaud (DocFX).

This works with Cassandra 3.X. Should work with 4.X+.

The purpose was to create atomic-small programs/scripts in several languages to do the same thing:
- Connect to a Cassandra cluster (or single server), generated by Docker
- Create a keyspace
- Create a table
- Insert some data
- Select that data
- Display the selected data in a basic table output in the console 

## Before you start working

Fix the EOL to Linux-style, that's better for everyone.
```shell script
git config core.eol lf
git config core.autocrlf input
```

## Docker configuration
- Enable HyperThreading / SMT in your MB BIOS
- Enable Hyper-V in Windows (if needed)
- Set up the volume sharing base directory (one at least), like `C:\docker-volumes` and...
- Add the `/docker-volumes` directory (or the base drive at least) of this directory to the volume sharing

Apache Cassandra Docker Hub info: https://hub.docker.com/_/cassandra

## Docker 

### Simple creation of three independent servers

Creates three containers, one by one:
```shell script
docker network create cassandra-network
docker run --name cassandra-server-1 --network cassandra-network -d -p 9042:9042 -v C:\docker-volumes\cassandra-server-1:/var/lib/cassandra cassandra:latest
docker run --name cassandra-server-2 --network cassandra-network -d -p 9043:9042 -v C:\docker-volumes\cassandra-server-2:/var/lib/cassandra cassandra:latest
docker run --name cassandra-server-3 --network cassandra-network -d -p 9044:9042 -v C:\docker-volumes\cassandra-server-3:/var/lib/cassandra cassandra:latest
```

Run the CQL Shell of each container:
```shell script
docker run -it --network cassandra-network --rm cassandra cqlsh cassandra-server-1
docker run -it --network cassandra-network --rm cassandra cqlsh cassandra-server-2
docker run -it --network cassandra-network --rm cassandra cqlsh cassandra-server-3
```

### Advanced creation of a cluster of three Cassandra servers

How it was built:
```shell script
docker run --rm -d --name tmp cassandra:latest
docker cp tmp:/etc/cassandra etc_cassandra-latest_vanilla
docker stop tmp
```

## Starting the cluster:

Cassandra 4.X (single server):
```shell script
docker-compose -f cassandra4-1-server-cluster-docker-compose.yml up -d --force-recreate
```

Cassandra 4.X:
```shell script
docker-compose -f cassandra4-3-servers-cluster-docker-compose.yml up -d --force-recreate
```

Cassandra 3.X:
```shell script
docker-compose -f cassandra3-3-servers-cluster-docker-compose.yml up -d --force-recreate
```
**NOTE** you need to change the scripts according to the local ports if you change Cassandra Version to 3. 


Check the nodes are running and OK (replace "X" with the version chosen above):
```shell script
docker exec cassandraX-cluster-server-1 nodetool status
docker exec cassandraX-cluster-server-2 nodetool status
docker exec cassandraX-cluster-server-3 nodetool status
```

Run the CQL Shell of each container (replace "X" with the version chosen above):
```shell script
docker exec -it cassandraX-cluster-server-1 cqlsh
docker exec -it cassandraX-cluster-server-2 cqlsh
docker exec -it cassandraX-cluster-server-3 cqlsh
```

## Expected structures and CQL + sample data

Those are the same queries executed by those atomic-small programs.

```SQL
describe keyspaces;

create keyspace if not exists xx_demo with replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

use xx_demo;

describe tables;

create table if not exists xx_demo.xx_demotable(id uuid, name text, value int, maps map<int, text>, primary key ((id), name, value));

describe xx_demo;

truncate xx_demo.xx_demotable;
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 1', 1, {1: 'value 1', 2: 'value 2 '});
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 2', 2, {1: 'value 1', 2: 'value 2 '});
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 3', 3, {1: 'value 2', 2: 'value 3', 4: 'value 4'});
```


## Java

The project uses Maven and Java 15+.

How it is built:
```shell script
mvn clean compile assembly:single
```

To run the program:
```shell script
cd java
java -cp target/cpw-1.0-jar-with-dependencies.jar App
```

## Go

The project uses the GoCQL connector library and Go 1.15+.

How it is built:
```shell script
go get github.com/gocql/gocql
go mod init cassandra-pw.go
```

To run the program:
```shell script
cd go
go run cassandra-pw.go
```

## JavaScript

The project uses NodeJS 15+ and NPM.

How it is built:
```shell script
npm init
npm install chalk
npm install cassandra-driver
```

To run the program:
```shell script
cd javascript
go node index.js
```


## Python

The project uses Python 3.8+.

Windows users, if needed (if Python can't find packages):
```shell script
pip uninstall virtualenv
pip uninstall pipenv
pip install pipenv
```

How it is built:
```shell script
pipenv install cassandra-driver
pipenv install colorama
```

To run the program:
```shell script
cd python
pipenv run python cassandra-pw.py
```

![MongoDB Logo](mongodb-logo.png)


# MongoDB Practical Works - template
A 101 repository containing the template for practical works to use MongoDB (Community Edition) on different languages.

This repository was created to help students using MongoDB at IUT Limoges, by William Pinaud (DocFX).

This works with MongoDB 6.0+.

The purpose was to create atomic-small programs/scripts in several languages to do the same thing:
- Connect to a MongoDB cluster (or single server), generated by Docker
- Create a database
- Create a collection
- Insert some data
- Select that data
- Display the selected data in a basic table output in the console

## Docker

### Simple creation of three independent servers

Creates three containers, one by one:
```shell script
docker network create mongodb6-network
docker run --name mongodb6-server-1 --network mongodb6-network -d -p 27017:27017 -v C:\docker-volumes\mongodb6-server-1:/var/lib/mongodb mongo:6
```

Run the MongoDB Shell of each container:
```shell script
docker exec -it mongodb6-cluster-server-1 mongosh
```

### Advanced creation of a cluster of just one MongoDB server

How it was built:
```shell script
docker run --rm -d --name tmp mongo:6
docker cp tmp:/etc/mongodb etc_mongodb-latest_vanilla
docker stop tmp
```

## Starting the cluster:

MongoDB 6.X:
```shell script
docker-compose -f mongodb6-1-server-cluster-docker-compose.yml up -d --force-recreate
```

## Expected structures and CQL + sample data

Those are the same queries executed by those atomic-small programs.

```SQL
describe keyspaces;

create keyspace if not exists xx_demo with replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

use xx_demo;

describe tables;

create table if not exists xx_demo.xx_demotable(id uuid, name text, value int, maps map<int, text>, primary key ((id), name, value));

describe xx_demo;

truncate xx_demo.xx_demotable;
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 1', 1, {1: 'value 1', 2: 'value 2 '});
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 2', 2, {1: 'value 1', 2: 'value 2 '});
insert into xx_demo.xx_demotable(id, name, value, maps) values (uuid(), 'test 3', 3, {1: 'value 2', 2: 'value 3', 4: 'value 4'});
```


## PHP

The project uses Composer and PHP 8+

How it is built:
```shell script
composer update
````

To run the program:
```shell script
cd php
php mongodb-pw.php
```
