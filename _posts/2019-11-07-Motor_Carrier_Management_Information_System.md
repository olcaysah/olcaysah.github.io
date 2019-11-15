---
layout: archive
title: "Motor Carrier Management Information System"
modified: 2019-10-22
comments: true
share: true
---

RShiny application for downloading and visualizing Motor Carrier Management Information System (MCMIS) data.
More information can be found here: [link](https://ask.fmcsa.dot.gov/app/mcmiscatalog/d_census_mcmis_doc){:target="_blank"}

I wanted to have an example for downloading data from RShiny and PostgreSQL powered web application.

Using this application, data can be filtered and downloaded.

I will also include some simple analysis.

Data is open to public and can be downloaded from this [link](https://ai.fmcsa.dot.gov/SMS/Tools/Downloads.aspx){:target="_blank"}.
This data updates every month. I downloaded October 2019 dataset.
If I have time, I will write a PHP script for automatically downloads and updates previously created database.

## Virtal Private Server Setup
I rented a low cost virtual private server from OVH ($4 per month (a cup of coffee!)) in the following configuration 1 vCore 2GB memory and 20GB SSD space.

## Database Setup
I created a local PostgreSQL database in the server for this data set. I could also use real-time reading from file using data.table library, but it could take some time to read this data from file in this server. This server is a low-cost server for lightweight works.

So I created a table in the database. You can see the SQL file in the repository.

## Shiny Server Setup
Shiny server installation is straightforward. Just follow steps from official RStudio Shiny tutorial: [link](https://rstudio.com/products/shiny/download-server/ubuntu/){:target="_blank"}

## Database Credentials protection
Since I am going to upload this script to the Github public repository, I am going to hide my credentials. In order to do so, I created a .Renviron file and included necessary credentials in this file as the following:
```
dbname = "name"
dbuser = "user"
dbpass = "password"
dbhost = "Ip address or localhost"
dbport = 5432 #Generally port for PostgreSQL is 5432. If you have multiple version check port number in postgres config
dbtable = "table name" #Not really necessary if you have multiple table in your database.
```
When an application or RStudio get started, these information are loaded to environment. There are so many ways to protect your credentials which is explained detail in Rstudio's maual page: [link](https://db.rstudio.com/best-practices/managing-credentials/){:target="_blank"}

## NGINX Web Server
Shiny server has it own port number (3838) to serve its applications. However I don't want to enable any port other than 80. Therefore, I setup a proxy in Nginx web server. In this way, you can set a subdomain from domain name provider (e.g., GoDaddy, Name.com, etc) and point to Virtual Server IP address. Since we added the proxy information to the NGINX server, it will handle the routing.

Anybody who are not familier with the server and proxy can copy the below configuration. Don't forget to update your information.

Below file is located at /etc/nginx/sites-available/mcmis

```
server {
    server_name mcmis.olcaysahin.com;
    root /opt/shiny-server/samples/mcmis/;
    gzip on;
    gzip_types text/plain text/css application/xml application/x-javascript;
    location / {
        proxy_set_header X-Real-IP  $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $host;
        proxy_buffering off;
        proxy_read_timeout 300s;
        proxy_pass http://127.0.0.1:3838/mcmis/;
        client_max_body_size 1000m;
     }
}
```

## Symbolic Link
As you can see from the above root folder location, created RShiny Application located in a secure location. Therefore this location must be linked using Linux "Symbolic Link" command. See the example below:
```
ln -s /opt/shiny-server/samples/mcmis/ /srv/shiny-server/
```

Now the application can be seen under my porfolio's subdomain as: [http://mcmis.olcaysahin.com](http://mcmis.olcaysahin.com){:target="_blank"}
