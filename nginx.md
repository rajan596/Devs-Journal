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


### Configurations and Architecture

#### Processes
By default, *Nginx starts one master process and one worker process.* The master process is responsible for managing worker processes, while the worker process handles incoming connections.

*Master process:*
The master process is responsible for reading the configuration file and starting worker processes. It also monitors worker processes and restarts them if they crash.

*Worker process:* 
Each worker process handles incoming connections and serves requests. By default, Nginx starts one worker process, but you can increase this number by setting the worker_processes directive in the nginx.conf file.

#### worker_connections
> No. of max connections a single process can open simultaneously.

worker_connections is a configuration directive in the Nginx web server that determines the maximum number of connections that can be opened simultaneously by each worker process.

Here's how it works:

Nginx uses a multi-process architecture, where multiple worker processes handle incoming connections. When a client makes a request to the server, one of the worker processes accepts the connection and serves the request.

The worker_connections directive specifies the maximum number of simultaneous connections that each worker process can handle. For example, if you set worker_connections to 1024, each worker process can handle up to 1024 connections at the same time.

It's important to set this value carefully, as it can affect the server's performance and stability. If you set worker_connections too low, your server may not be able to handle a high volume of incoming connections, which can lead to slow response times or dropped connections. On the other hand, if you set worker_connections too high, your server may run out of system resources such as file descriptors or sockets, which can cause it to crash.

In general, you should set worker_connections based on the amount of memory and processing power available on your server, as well as the expected volume of incoming connections. It's also a good idea to monitor your server's resource usage and adjust worker_connections as needed to ensure optimal performance.

#### proxy_pass
proxy_pass is an Nginx directive that is used to redirect client requests to a backend server. It is often used to set up reverse proxy servers that act as intermediaries between clients and backend servers.

#### Variables in nginx
- $remote_addr: IP address of the client that made the request.
- $proxy_add_x_forwarded_for : used to add the IP address of the client to the X-Forwarded-For header when proxying requests.
- $scheme : Nginx variable that represents the scheme (protocol) of the request (e.g. http or https).
- 

### Use nginx for okta authentication
- https://docs.nginx.com/nginx/deployment-guides/single-sign-on/okta/ : This is using nginx plus which is advanced and paid version of open source nginx

- Integrate Okta : https://developer.okta.com/blog/2018/08/28/nginx-auth-request

- Build your own version from source code: https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/

