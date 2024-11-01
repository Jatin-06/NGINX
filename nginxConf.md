user nginx;
worker_processes auto;
worker_rlimit_nofile 8192;
pid /run/nginx/nginx.pid;

events {
    worker_connections 4000;
}

error_log /var/log/nginx/error.log error;

http {
    access_log off;
    server_tokens "";
    upstream app1 {
     server 172.16.11.4:443;
    } 
 
  server {
      server_name ecm.maxwellclaims.net;
      listen 443 ssl;                                      // listening to 443port
      ssl_certificate /etc/nginx/maxwellclaims.cert;
      ssl_certificate_key /etc/nginx/maxwellclaims.key;

     location / {
        proxy_pass https://app1;                 //nginx 
        proxy_set_header X-Real-IP  $remote_addr;          // it captures the origin ip of requester and forward to backend servers
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_set_header Host $host;                      //nginx replaces the host reader $host with variable then it sent reques to the backend server
    }  
 
  }


    server {                                              //this block is for nginx webserver
        listen 80 default_server;
        server_name localhost;
        location / {
            # Points to a directory with a basic html index file with
            # a "Welcome to NGINX as a Service for Azure!" page
            root /var/www;
            index index.html;
        }
    }
}




