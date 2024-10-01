## NGINX CONFIGURATION

Step 1: Install nginx web server
- See nginx official documentation as per your OS.

Step 2: Configure DNS
- domain name - example.com

- file name : example.com.conf
  `server{

        listen 80;
        server_name example.com;

        location / {
                proxy_pass "http://127.0.0.1:8080";
                proxy_set_header Host example.com;
        }
}`

## Load Balancing Conf
  ```
        upstream backend_servers{

                # IP Hash – The server to which a request is sent is determined from the client IP address.
                ip_hash;

                # load balancing servers
                server 127.0.0.1:8081;
                server 127.0.0.1:8082;
                server 127.0.0.1:8083;
        }



        server{

                listen 80;
                server_name example.com;

                location / {
                        proxy_pass http://backend_servers;
                        #proxy_set_header Host $host;
                        proxy_set_header Host example.com;
                }
        }


  ```
