Nginx (pronounced "engine-x") is a high-performance, open-source web server and reverse proxy that is designed to handle a large number of simultaneous connections. It is often used to serve static content, reverse proxy requests to other servers, and load balance traffic across multiple servers.

Here are some basic concepts and commands that will help you get started with Nginx:

Nginx Configuration: Nginx's configuration file is located at /etc/nginx/nginx.conf on most Unix-based systems. You can modify this file to control how Nginx behaves.

Nginx Directives: Nginx uses a series of directives to configure its behavior. Directives are organized into blocks, which are delimited by curly braces. Some commonly used directives include server, location, proxy_pass, and root.

Nginx Server Blocks: Nginx uses server blocks to define how it should handle requests for different domains or IP addresses. Each server block can contain one or more location blocks, which define how Nginx should handle requests for specific URLs.

Nginx Reverse Proxy: Nginx can be used as a reverse proxy to route requests to other servers. To set up a reverse proxy, you'll typically use the proxy_pass directive to specify the URL of the upstream server.

Nginx Load Balancing: Nginx can also be used to balance traffic across multiple servers. You can configure Nginx to distribute requests among a group of servers using a variety of load-balancing algorithms.

Nginx Logging: Nginx logs all requests and responses by default. You can use the access_log directive to specify where Nginx should write its logs.

Nginx Security: Nginx includes a number of features to help you secure your web server. For example, you can use the ssl_certificate and ssl_certificate_key directives to configure SSL/TLS encryption, and the deny and allow directives to control access to your server.

Here are some basic commands that you can use to interact with Nginx:

```bash
sudo systemctl start nginx    # start the Nginx service
sudo systemctl stop nginx     # stop the Nginx service
sudo systemctl restart nginx  # restart the Nginx service
sudo systemctl reload nginx   # reload the Nginx configuration without restarting the service
sudo nginx -t                 # test the Nginx configuration for syntax errors
sudo nginx -s reload          # reload the Nginx configuration
```

$http_authorization is an nginx variable that contains the value of the HTTP Authorization header from the client's request.

```nginx

events {
    worker_connections  1024;
}

http {
  server {
    listen 80;                                    # acts as a web server and listens api requests on 80 port.
    server_name loca-seer-stormui.com;
    
    location / {                                  # all requests coming after / will be using this location block.
      proxy_pass http://localhost:8082;           # pass proxy to actual server
      access_log /usr/local/etc/nginx/access.log; # logs
    }
  }
}
```

### Use nginx for okta authentication
https://docs.nginx.com/nginx/deployment-guides/single-sign-on/okta/
