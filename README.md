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

#### 6. SSL certificate creation with certbot

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
