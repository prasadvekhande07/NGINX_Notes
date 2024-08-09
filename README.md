### NGINX Overview

#### What is NGINX?
- **NGINX** is a powerful, high-performance web server that also functions as a reverse proxy, load balancer, and more.
  
#### Why was NGINX Created?
- Created to handle high traffic websites, providing efficient handling of multiple concurrent connections.

#### Functionality:
- **Web Server**: Serves web pages to clients' browsers.
- **Reverse Proxy**: Acts as an intermediary server that forwards requests to another server.
- **Load Balancer**: Distributes incoming traffic across multiple backend servers.

---

### Web Server Basics

- **Browser Request**: A browser requests a web page from a web server.
- **Web Server**: Software running on a machine that handles HTTP requests and serves web pages to the client's browser.
- **Primary Function**: Serve web pages to the client's browser.

---

### NGINX as a Load Balancer

- **Purpose**: Distribute incoming traffic across multiple backend servers to balance the load, improve performance, and provide redundancy.
- **Load Balancing Methods**:
  - **Least Connection**: Routes traffic to the server with the fewest active connections.
  - **Round Robin**: Distributes client requests sequentially in a cyclical manner to each server in a group.

---

### NGINX as a Proxy Server

- **Proxy Definition**: Acts on behalf of another; an intermediary server that forwards requests to another server.
- **Proxy Server Capabilities**:
  - **Load Balancing**
  - **Caching Data**: Stores copies of frequently accessed resources temporarily to improve performance.
  - **Security**: Reduces attack surface, consolidates security, and centralizes access control.
  - **Compression and Segmentation**: Compresses responses to reduce bandwidth usage and segments large files for better load times.

---

### Cache Management

- **Purpose**: Reduce response times by storing copies of data temporarily.
- **Caching in NGINX**: NGINX can cache responses from the backend server for frequently accessed resources, improving performance.

---

### Security in NGINX

- **Entrypoint Security**: Reduces attack surface by having only one publicly available server.
- **SSL Termination**: NGINX can handle SSL/TLS encryption and decryption.
- **Enforce HTTPS**: Ensures encrypted traffic by accepting only HTTPS requests and denying non-encrypted requests.

---

### NGINX Configuration Overview

1. **Main Configuration File**: `nginx.conf` located in `/etc/nginx`.
   - Custom syntax comprising "Directives" and "Blocks".
   - Defines the server's behavior.

---

### Example Configurations

#### 1. **Serving Files as a Web Server**:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    location / {
        root /var/www/example.com; 
        index index.html index.htm;
    }
}
```
- **Location Directive**: Specifies the root directory containing the files to be served.

#### 2. **Forwarding Traffic as a Proxy Server**:

```nginx
server {
    listen 80;
    server_name api.example.com;
    location / {
        proxy_pass http://backend_server_address;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### 3. **Redirect HTTP to HTTPS**:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;
}
```
- Redirects all HTTP requests to HTTPS.

#### 4. **Serve Content over HTTPS**:

```nginx
server {
    listen 443 ssl;
    server_name example.com www.example.com;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    location / {
        root /var/www/example.com; 
        index index.html index.htm;
    }
}
```

---

### Load Balancing Configuration

```nginx
http {
    upstream myapp1 {
        server srv1.example.com; 
        server srv2.example.com; 
        server srv3.example.com;
    }
    server {
        listen 80;
        location / {
            proxy_pass http://myapp1;
        }
    }
}
```
- Default to the round-robin algorithm.

- **Least Connection Load Balancing**:

```nginx
upstream myapp1 {
    least_conn;
    server srv1.example.com; 
    server srv2.example.com; 
    server srv3.example.com;
}
```

---

### Enable Caching of Responses

```nginx
http {
    proxy_cache_path /data/nginx/cache keys_zone=mycache:10m;
    server {
        proxy_cache mycache;
        location / {
            proxy_pass http://localhost:8000;
        }
    }
}
```
- **Proxy Cache Path**: Defines the cache location and key zone.

---

### NGINX in Kubernetes as Ingress Controller

- **Ingress Controller**: A proxy with advanced load balancing functionality for managing incoming traffic in Kubernetes.
- **Advanced Load Balancer**: Handles routing to appropriate services based on rules defined in ingress resources.
