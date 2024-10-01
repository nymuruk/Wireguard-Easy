# WG-Easy WireGuard Server Setup on Ubuntu 22.04

To create a WG-Easy WireGuard server on your VPS (Ubuntu 22.04), follow the steps below:

1. Update Your System
First, ensure your system is up to date by running:

```
sudo apt update && sudo apt upgrade -y
```


2. Install Docker
WG-Easy uses Docker, so you need to install it first.

Install dependencies:

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
```


Add Docker’s official GPG key:

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Add Docker repository:

```
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install Docker:

```
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y
```

Enable Docker to start on boot:

sudo systemctl enable docker
sudo systemctl start docker     

Verify Docker is running:

```
sudo docker --version
``` 

1. Install Docker Compose
Run the following commands to install Docker Compose:

Download the latest version of Docker Compose:

```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

Apply executable permissions to the binary:

```
sudo chmod +x /usr/local/bin/docker-compose
```

Verify the installation:

```
docker-compose --version
```


3. Install WG-Easy Using Docker
Now, install WG-Easy using Docker.

Create a directory for WG-Easy configuration:

```
mkdir ~/wg-easy && cd ~/wg-easy
```

Create a docker-compose.yml file:

```
nano docker-compose.yml
```

Paste the following content into the docker-compose.yml file:
```
version: "3.8"

services:
  wg-easy:
    image: weejewel/wg-easy
    container_name: wg-easy
    environment:
      - WG_HOST=#your domain or ip
      - PASSWORD=#your password  # You can change this
    ports:
      - "51820:51820/udp"
      - "51821:51821/tcp"
    volumes:
      - ./config:/etc/wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
      - net.ipv6.conf.all.forwarding=1
    restart: unless-stopped
```

Replace your_domain_or_ip with your VPS's public IP address (your ip) or domain name (yourdomain.com).

4. Start WG-Easy
To launch the WG-Easy server, use Docker Compose:

```
sudo docker-compose up -d
```



This will pull the WG-Easy image and start the WireGuard server.

5. Access WG-Easy
Once the server is running, you can access the WG-Easy web interface at http://your_domain_or_ip:51821.
Username: admin
Password: sawatui (or the one you set in docker-compose.yml).

6. Enable WireGuard Kernel Module
To ensure WireGuard operates correctly, load the WireGuard module:

```
sudo modprobe wireguard
```

You can also make sure the WireGuard kernel module loads at boot:

```
echo "wireguard" | sudo tee /etc/modules-load.d/wireguard.conf
```


7. Firewall Settings (Optional)
If you have a firewall enabled, allow WireGuard traffic:

```
sudo ufw allow 51820/udp
sudo ufw allow 51821/tcp
```


8. Check Server Status
Check the status of your WG-Easy Docker container:

```
sudo docker ps
```


To view logs:

```
sudo docker logs wg-easy
```

9. Auto-start on Boot
Ensure the service starts automatically on boot:

```
sudo systemctl enable docker
```


Your WG-Easy server is now set up, and you can manage WireGuard users through the web interface.

If you need to modify the configuration or update it, you can edit the docker-compose.yml and restart the services using:

```
sudo docker-compose down
```
```
sudo docker-compose up -d
```
