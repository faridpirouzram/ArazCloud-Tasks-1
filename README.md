# ArazCloud-Tasks
##ArazCloud interview tasks.

## Task 2 - NGINX Load Balancer
We have 7 containers.
* 3 NGINX servers
  * erere
* 3 Prometheus exporters
* 1 Prometheus container.
We have a NGINX load balancer listening on port 80 and port 8080 for stub status module, and 443 for SSL. Stub status module exposes NGINX metrics. We also have 2 nginx containers exposing their metrics on port 8080 inside the task2-network network. Our NGINX load balancer is splitting the traffic between the 2 containers via Round-Robin algorithm and proxy-passing requests to them. These 3 nginx servers are exposing metrics on ip:8080/nginx_status url in task2-network.
We have a Prometheus container with 3 scraping configs for NGINX containers. These metrics are exported by 3 different nginx-prometheus-exporters. Our Grafana has 3 panels, one for container 1, another for container 2, and the last for for all of 3 NGINX servers combined.
The SSL Certificate for our NGINX server is self-signed. This certificate is built into nginx-ssl image before deploying the stack.
