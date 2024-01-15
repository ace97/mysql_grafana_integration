# Integrating Grafana with MySQL

A simple demonstation project to show how to set up Grafana & connect it with a database to create dashboards, here we'll be using MySQL as the database.
We are using AWS to create a server instance that hosts the required softwares and using docker containers to run each individual software.

## Requirements
Create AWS Linux EC2 instance (t2.micro with default settings is sufficient) and then install docker.
Also create a new inbound rule in the security group to access all traffic over IPv4 (although not the best practice when it comes to security for demonstration and learning purposes we are using this to avoid setting VPC and RBAC).
```console

root:~$ yum install docker –y
root:~$ systemctl start docker
root:~$ systemctl enable docker

```
## Install and setup Grafana as a Docker container
```console

root:~$ docker run -d --name=grafana -p 3001:3000 -v grafana_config:/etc/grafana -v grafana_data:/var/lib/grafana -v grafana_logs:/var/log/grafana grafana/grafana

```
Use default settings to login to grafana at 'EC2 Public-IP ':3001 (username: admin and pasword: admin ) and create a new password.

## Install and setup MySQL as a Docker container
```console

root:~$ docker run -d --name mysqldb -p 3306:3306 -v db_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password mysql:latest

```

Now enter the MySQL docker container in interactive bash mode.
```console

root:~$ docker exec -it mysqldb bash

```
Once inside the container log into the MySQL server created using username=root and password=password.
```console

root@container-id:/# mysql -u root -p # enter 'password' in the next line

```

In the MySQL bash console create a db and table.
```mysql
-- Create a new database
CREATE DATABASE IF NOT EXISTS team;

USE team;

-- Create a table
CREATE TABLE IF NOT EXISTS person (
  person_id INT NOT NULL PRIMARY KEY,
  fname VARCHAR(40) NULL,
  lname VARCHAR(40) NULL,
  age INT NOT NULL, created TIMESTAMP);

-- Insert data into the table
INSERT INTO person (person_id,fname,lname,age) VALUES
    (1,'Peter','Engineer',32),
    (2,'Richard','Gaol',27),
    (3,'Howard','Ken',38),
    (4,'Lucy','Dey',32);

-- Query the data to check values have been inserted succefully
SELECT * FROM users;

```

## Add MySQL as Data Source in Grafana

![Data Source](https://github.com/ace97/mysql_grafana_integration/tree/main/image/Grafana-dashboard.png )

Use the 'EC2 Public-IP' in place of localhost. Give team in database field.
Username = root
Password = password


## Create a Dahsboard in Grafana with Add Panel
Once inside the add panel page, write a query to fetch data from our table person to be displayed on the dashboard.

```mysql

Select * from team.person;

```
Now save the panel, give the dashboard a name. And finally you should have a dashboard that looks something like the image shown below.

![Dashboard](https://github.com/ace97/mysql_grafana_integration/tree/main/image/Grafana-dashboard.PNG)











