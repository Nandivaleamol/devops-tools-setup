# ZeroSSL Installation on UBUNTU OS NGINX

## To install a ZeroSSL certificate on an Ubuntu server with an Nginx web server, follow these steps:

### Prerequisites:
- Ubuntu server with Nginx installed.
- Domain name pointed to your server’s IP address.
- Access to your server via SSH.
- A valid ZeroSSL certificate (You can get one from the [ZeroSSL website](https://zerossl.com/)).

### Step 1: Generate a Certificate from ZeroSSL
1. Go to [ZeroSSL](https://zerossl.com/).
2. Select “New SSL Certificate” and choose the "90-Day Free SSL" option or the certificate plan of your choice.
3. Enter your domain name and select `Next`.
4. You may need to verify your domain via DNS, email, or file upload, depending on your domain setup.
5. After verification, ZeroSSL will provide the following files:
    - `Certificate.crt` (or `crt` file)
    - `CA Bundle` (Intermediate certificate)
    - `Private key`
  
### Step 2: Upload the Certificate and Key Files to Your Server
1. Copy the `Certificate.crt`, `Private.key`, and `ca_bundle.crt` files to your server using SCP, FTP, or any preferred method.
    ```
    scp /path/to/your/Certificate.crt username@your_server_ip:/etc/nginx/ssl/yourdomain.crt
    scp /path/to/your/Private.key username@your_server_ip:/etc/nginx/ssl/yourdomain.key
    scp /path/to/your/ca_bundle.crt username@your_server_ip:/etc/nginx/ssl/ca_bundle.crt
2. Create an `ssl` directory if it doesn't exist on your server:
     ```
    sudo mkdir -p /etc/nginx/ssl
    sudo chmod 700 /etc/nginx/ssl
Step 3: Configure Nginx to Use the SSL Certificate
1. Open the Nginx configuration file for your site. If your site is located at `/etc/nginx/sites-available/yourdomain`, run:
   ```
   sudo nano /etc/nginx/sites-available/yourdomain

2. Update the Nginx configuration file with the SSL settings:
   ```
   server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    
    # Redirect all HTTP requests to HTTPS
    return 301 https://$server_name$request_uri;
    }

    server {
        listen 443 ssl;
        server_name yourdomain.com www.yourdomain.com;

        ssl_certificate /etc/nginx/ssl/yourdomain.crt;
        ssl_certificate_key /etc/nginx/ssl/yourdomain.key;
        ssl_trusted_certificate /etc/nginx/ssl/ca_bundle.crt;

        ssl_protocols TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;
        ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH";
        ssl_session_timeout 1d;
        ssl_session_cache shared:SSL:10m;
        ssl_stapling on;
        ssl_stapling_verify on;

        root /var/www/yourdomain;
        index index.html index.htm index.nginx-debian.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }

3. Save and exit the file.
   
### Step 4: Test Nginx Configuration
Test if the Nginx configuration is correct:
```
sudo nginx -t
```

If there are no errors, reload Nginx to apply the changes:
```
sudo systemctl reload nginx
```

### Step 5: Verify SSL Installation
To verify that your SSL certificate is installed correctly:
- Visit `https://yourdomain.com` in a browser.
- You can also use SSL verification tools like `SSL Labs SSL Test` to ensure everything is configured properly.
  

Your ZeroSSL certificate should now be successfully installed and running on Nginx.
   