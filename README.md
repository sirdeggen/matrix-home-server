# Matrix Home Server Installation

#### 1. Set up any Droplet which can run Docker and Nginx

For example you can set something up on [Digital Ocean](https://www.digitalocean.com/): https://www.youtube.com/watch?v=cMD6_MwgHDo

I used an Ubuntu Droplet from Digital Ocean.

#### 2. Connect a Domain with DNS records

An "A" record pointing from "matrix.YOURDOMAIN.com" to your droplet IPv4 address.

#### 3. Clone this repo into your Droplet

```bash
git clone https://github.com/sirdeggen/matrix-home-server.git
```

#### 4. Edit the config file with your domain name

Lines 36 and 57 of `./config/dendrite.yaml` you should replace YOURDOMAIN with whatever your domain is.

#### 5. Set up nginx to point to your domain

Take this file but update the YOURDOMAIN parts to your domain, and then copy the contents.
```
server {
    listen 80;
    listen [::]:80;

    root /var/www/html;
    index index.html ;
    server_name matrix.<yourdomain>.com;

    proxy_set_header Host      $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_read_timeout         600;

    location /.well-known/matrix/server {
        return 200 '{ "m.server": "matrix.YOURDOMAIN.com:443" }';
    }

    location /.well-known/matrix/client {
        # If your sever_name here doesn't match your matrix homeserver URL
        # (e.g. hostname.com as server_name and matrix.hostname.com as homeserver URL)
        add_header Access-Control-Allow-Origin '*';
        return 200 '{ "m.homeserver": { "base_url": "https://matrix.YOURDOMAIN.com" } }';
    }

    location /_matrix {
        proxy_pass http://localhost:8008;
    }
}
```

Run the following commands, using `vim` to paste the file above in (in vim you hit "a" to append data, then "Ctrl" & "P" to paste the file content, hit "Esc" and then type ":x" to save the content. Again make sure to replace YOURDOMAIN.com with your actual domain so the filename matches.

```bash
rm /etc/nginx/sites-available/default
rm /etc/nginx/sites-enabled/default
vim /etc/nginx/sites-available/YOURDOMAIN.com
ln -s /etc/nginx/sites-available/YOURDOMAIN.com /etc/nginx/sites-enabled/YOURDOMAIN.com
```

The last line just symlinks the available file to the enabled file, allowing you to take individual sites on and offline.

#### 6. SSL certificate creation with certbot

Install and use certbot
```bash
sudo apt install certbot python3-certbot-nginx
certbot --nginx -d matrix.YOURDOMAIN.com
```

#### 7. Create matrix keys

Make sure you're in the conifg folder of your cloneds repo, then run:

```bash
docker run --rm --entrypoint="" -v $(pwd):/mnt matrixdotorg/dendrite-monolith:latest \
   /usr/bin/generate-keys \
   -private-key /mnt/matrix_key.pem \
   -tls-cert /mnt/server.crt \
   -tls-key /mnt/server.key
```

#### 8. Run the Docker Images

```bash
docker-compose up -d
```
