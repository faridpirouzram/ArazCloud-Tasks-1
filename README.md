# ArazCloud-Tasks
## Task 1 - Wordpress with Postgresql and NGINX
####How Wordpress and Posgtresql are installed
Wordpress and PostgreSQL are working together with the help of a fork of [PG4WP](https://github.com/kevinoid/postgresql-for-wordpress).
After downloading and extracting Wordpress and PG4WP in `/var/ww/html/wordpress` directory, we will copy `pg4wp` directory to `.../wordpress/wp-content/`, and also `pg4wp/db.php` to `.../wordpress/wp-content/`.
After that, we'll create a database, a user with a password in `psql` command. And we'll add these information in `.../wordpress/wp-config.php` file.
Then we need to install **php** and **php-fpm** and add php configuration to NGINX config file.
####NGINX configuration
We want NGINX to interact with our wordpress, so we configure it to work with php files with help of **php-fpm**. NGINX is also configured to redirect HTTP to HTTPS and has SSL certificate.
## Task 2 - NGINX Load Balancer
For this task, we have 7 containers and 1 grafana server working together.
The containers are:
* **3** NGINX server containers
  * **1** NGINX load balancer, listening on port **80** and **443**
  * **2** NGINX web servers
* **3** Prometheus exporter containers
	* **1** exporting load balancer metrics
	* **2** exporting web servers metrics
* **1** Prometheus container
	*  listening on port **9090**

####Docker-stack.yml
Services:
* nginx_balancer
	* mapping ports **80:80** and **443:443**
	* mounting `./nginx-load-balancer/` directory for NGINX config files, for load balancing, ssl, stub status module and proxy pass configs.
* nginx_containers
	* mounting `./nginx_containers/` for nginx config file, for stub status module.
* prometheus
	* mapping port **9090:9090**
	mounting `./prometheus/` directory for prometheus config file, scraping data from 3 exporters
* exporter_nginx containers
	* exporting metrics from nginx containers
	
#### Load Balancer
NGINX load balancer is a container, proxy passing requests to NGINX container 1 and 2. The load balancing algorithm is **Round-Robin**.
####How metrics are exported
NGINX servers listen on **8080/nginx_status** in docker local network named "task2-network" for exposing metrics with the help of ***Stub Status module***. Prometheus exporter containers are extracting metrics from this url. After that, the Prometheus container scrapes mertics from exporters.
####Grafana
Grafana, installed on the host and listening on port **3000**, accepts data from the Prometheus container and displays them on created panels. There are 3 different panels:
* total number of load balancer accepted connections
* number of proxied requests to container 1
* number of proxied requests to container 2

####SSL
SSL certificate is created with the help of [Let's Encrypt](https://letsencrypt.org/) and [Certbot](https://github.com/certbot/certbot).
NGINX load balancer redirects HTTP traffic to HTTPS.



## Task 3 - Background changer
####How the playbook works
We have an ansible playbook with the ability to change background of our web server by changing the value of color in `index.html` file.
This playbook starts by searching for the color value using `sed` shell command, and stores the stdout in an ansible fact. After that, there are two ansible tasks, first one is changing background to green when it is blue, and the last task changes background to blue when it is green.
####Docker-stack.yml
We have a `docker-stack.yml` file that runs a webserver container and mounting `./data/` directory as a volume. Inside `./data/` directory is the `index.html` file that our playbook changes its color value.
