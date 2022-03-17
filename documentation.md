### **Docker Installation (Native)**

Reference: 
https://docs.docker.com/engine/install/ubuntu/ \
Original documentation from Docker, Inc.

Docker is a container runtime service. Main objects include images, volumes, networks, and containers.

Bash script generated from the instructions given in the installation guide for Ubuntu Distributions: \
(Follow the steps in bash script or run the entire bash script.)
```bash
#!bin/sh

# Uninstall old versions
sudo apt-get remove docker docker-engine docker.io containerd runc

# Set up the repository
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Add Docker's official GPG key:

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Set-up stable repository

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update

sudo apt-get install \
    docker-ce \
    docker-ce-cli \
    containerd.io

# Optional post-installation steps - to use docker as non-sudo user

# Create a docker group
sudo groupadd docker 

# Add your user to the docker group
sudo usermod -aG docker $USER

# Activate canges to groups
newgrp docker
```

### **Docker compose installation (Native)**

Reference: https://docs.docker.com/compose/install/ \
Original documentation from Docker, Inc.

We can create container-networks very easily with docker-compose. They are usually called as stacks. 

(Follow the steps in bash script or run the entire bash script.)
```bash
#!/bin/sh

# Run this command to download the current stable release of Docker compose:

sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Apply executable permissions to the binary:

sudo chmod +x /usr/local/bin/docker-compose

# Test the installation 
docker-compose --version
```
### **Portainer Installation (Docker)**

Resource: https://docs.portainer.io/v/ce-2.11/start/install/server/docker/linux

**Docker CLI**
```bash
docker run -d \
    --name portainer \
    --restart=always \
    -p 8000:8000 \
    -p 9443:9443 \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v <path to data>:/data \
    portainer/portainer-ce:2.11.1
```
Access portainer at https://localhost:9443/

### **Guacamole Installation (Docker)**

Reference:
https://github.com/boschkundendienst/guacamole-docker-compose 

Guacamole is used as a Remote Desktop Gateway. We can access other machines through RDP, VNC or SSH protocols.

Implementation of Guacamole was done through the resources provided in the Reference mentioned above. This project needed the Guacamole access through SSL encryption for security reasons. But the original guacamole/guacamole image is open to the port 8080/tcp. For this an nginx reverse-proxy is used. Nginx instance listens to 443 and provides Self-signed SSL certificates (in this case, for now), and reverse-proxies all the requests to guacamole/guacamole container's 8080/tcp port. \
\
For the initial setup we need to run the prepare.sh script in the choosen working directory of choice. Then we can run docker-compose.yml file to spin-up the container network for Guacamole. \

    docker-compose up -d

We can get the Guacamole at: https://localhost/.

The default username and password is "guacadmin".
Please change the user immediately after after first login. 

To change the user follow the below steps:

1. Log into the https://localhost/. 
2. Goto settings (top right corner).
3. Select users section.
4. Select "guacadmin" user.
5. Clone the user (options at the bottom).
6. Change the username and password for the new user.
7. Save the user.
8. Logout.
9. Login using new username and password. 
10. Go to users section in the settings. 
11. Select the "guacadmin" user.
12. Delete the user (options at the bottom).

To add new connection, follow the steps below:

1. Go to settings of Guacamole.
2. Go to connections.
3. Clock "New Connection" to add new connection. 
4. Important things to add are, protocol, machine name, host ip, port (RDP: 3389, VNC: 5900, SSH: 22), username and password of user account of machine. 
5. Other settings include display settings, sound settings etc. 
6. It is necessary to provide Remote control/Remote screen sharing facility in the client machine. 
7. After adding necessary settings, save the connection. 
8. Go to Home. 
9. Select the required connection in the "All connections" section of Home page. 
10. If done properly, the Remote Desktop of selected machine will appear on the screen upon clicking the machine name. 

### **Heimdall Installation (Docker)**
Resource: https://docs.linuxserver.io/images/docker-heimdall

Heimdall is used as a Dashboard for services. 

**Docker CLI**
```bash
docker run -d \
    --name=heimdall \
    --restart=unless-stopped \
    -p 80:80 \
    -p 443:443 \
    -v <path to config>:/config \
    lascr.io/linuxserver/heimdall
```

**Docker Compose**
```yaml
---
version: "2.1"
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall
    container_name: heimdall
    environment:
    - PUID=1000
    - PGID=1000
    - TZ=Eurole/London
    volumes:
    - <path to config>:/config
    ports:
    - 80:80
    - 443:443
    restart: unless-stopped
```
### **Wireguard Installation (Docker)**
Resource: https://docs.linuxserver.io/images/docker-wireguard

Wireguard is an alternative to OpenVPN. It is a cross-platform and widely deployable VPN server. 

**Docker CLI**
```bash
docker run -d \
  --name=wireguard \
  --cap-add=NET_ADMIN \
  --cap-add=SYS_MODULE \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Europe/London \
  -e SERVERURL=wireguard.domain.com `#optional` \
  -e SERVERPORT=51820 `#optional` \
  -e PEERS=1 `#optional` \
  -e PEERDNS=auto `#optional` \
  -e INTERNAL_SUBNET=10.13.13.0 `#optional` \
  -e ALLOWEDIPS=0.0.0.0/0 `#optional` \
  -p 51820:51820/udp \
  -v <path to config>:/config \
  -v /lib/modules:/lib/modules \
  --sysctl="net.ipv4.conf.all.src_valid_mark=1" \
  --restart unless-stopped \
  lscr.io/linuxserver/wireguard
```
**Docker Compose**
```yaml
---
version: "2.1"
services:
  wireguard:
    image: lscr.io/linuxserver/wireguard
    container_name: wireguard
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
      - SERVERURL=wireguard.domain.com #optional
      - SERVERPORT=51820 #optional
      - PEERS=1 #optional
      - PEERDNS=auto #optional
      - INTERNAL_SUBNET=10.13.13.0 #optional
      - ALLOWEDIPS=0.0.0.0/0 #optional
    volumes:
      - /path/to/appdata/config:/config
      - /lib/modules:/lib/modules
    ports:
      - 51820:51820/udp
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1
    restart: unless-stopped
```

### **Rancher Install (Docker)**
Resource: https://rancher.com/docs/rancher/v2.5/en/installation/other-installation-methods/single-node-docker/

Rancher is a Kubernetes cluster management tool. It implements K3s clusters and provides a web UI to manage and monitor all cluster activity.

**Docker CLI**
```bash
docker run -d \
    --name=rancher \
    --restart=unless-stopped \
    -p 80:80 \
    -p 443:443 \
    --privileged \
    rancher/rancher:latest
```
Rancher can be accessed from: https://localhost/ \
Password to initial login can be accessed via CLI with the command:

    docker logs <rancher-container-name> 2>&1 | grep "Bootstrap Password:"

To create a Cluster:

1. Go to cluster management.
2. Add cluster.
3. Select "custom cluster" option.
4. Select node options and generate CLI command.
5. Run the rancher generated CLI command in the intended node to cleat a cluster in it. 

To remove a cluster node manually:
https://rancher.com/docs/rancher/v2.5/en/cluster-admin/cleaning-cluster-nodes/ \

Remove node in Rancher UI and then run the below commands:
```bash
# To clean Docker containers, images and volumes:
docker rm -f $(docker ps -aq)
docker rmi -f $(docker images -q)
docker volume rm $(docker volume ls -q)

# To unmount all mounts:
for mount in $(mount | grep tmpfs | grep '/var/lib/kubelet' | awk '{ print $3 }') /var/lib/kubelet /var/lib/rancher; do umount $mount; done

# To remove directories and files:
rm -rf \
    /etc/ceph \
    /etc/cni \
    /etc/kubernetes \
    /opt/cni \
    /opt/rke \
    /run/secrets/kubernetes.io \
    /run/calico \
    /run/flannel \
    /var/lib/calico \
    /var/lib/etcd \
    /var/lib/cni \
    /var/lib/kubelet \
    /var/lib/rancher/rke/log \
    /var/log/containers \
    /var/log/kube-audit \
    /var/log/pods \
    /var/run/calico
```
If we don't properly clean a node, we can't register the node again. We will get errors. 

### **Uptime-Kuma installation (Docker)**
Resource: https://github.com/louislam/uptime-kuma/wiki/🔧-How-to-Install 

Uptime-Kuma is a service monitoring tool. It monitor's a service uptime and downtimes, alerts when services are down or crashed and provides some useful statistics. We can monitor any number of services with this tool. 

**Docker CLI**
```bash
docker run -d \
    --name uptime-kuma \
    --restart=unless-stopped \
    --security_opt=no-new-privileges \
    -p 3001:3001 \
    -v <path to app data>:/app/data \
    louislam/uptime-kuma:1
```
**Docker Compose**

```yaml
version: "3"
services:
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime-kuma
    volumes:
      - <path to app data>:/app/data
    ports:
      - "3001:3001"
    restart: unless-stopped
    security_opt:
      - no-new-privileges: true
```

### **Grafana Loki Installation (docker-compose)**
Resource: https://grafana.com/docs/loki/latest/installation/docker/

Grafana Loki is a log-aggregation tool. We can have machine, docker container or kubernetes cluster logs, all at one place with this service. Not just these application, it can scrape logs from any system with syslog functionality. Navigration through logs is very easy with Grafana Loki.

**docker-compose.yml**
```yaml
version: "3"
networks:
  loki:
services:
  loki:
    image: grafana/loki:2.4.0
    volumes:
      - <loki/config/directory>:/etc/loki
    ports:
      - "3100:3100"
    restart: unless-stopped
    command: -config.file=/etc/loki/loki-config.yml
    networks:
      - loki
  promtail:
    image: grafana/promtail:2.4.0
    volumes:
      - /var/log:/var/log
      - <promtail/config/directory>:/etc/promtail
    restart: unless-stopped
    command: -config.file=/etc/promtail/promtail-config.yml
    networks:
      - loki
  grafana:
    image: grafana/grafana:latest
    user: "1000"
    volumes:
    - <grafana/config/directory>:/var/lib/grafana
    ports:
      - "3000:3000"
    restart: unless-stopped
    networks:
      - loki
```

**loki-config.yml**
```yaml
auth_enabled: false
server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
    replication_factor: 1
    ring:
      instance_addr: 127.0.0.1
      kvstore: 
        store: in memory

schema_config:
  configs:
    - from: 2020-10-24
      store: boltdb-shipper
      object_store: filesystem
      schema: v11
      index:
        prefix: index_
        period: 24h

rules:
  alertmanager url: http://localhost:9093
```

**promtail-config.yml**
```yaml
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yml

clients:
  - url: http://loki:3100/loki/api/v1/push

# local machine logs
scrape_configs:
job_name: local
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log

# docker logs    
# scrape_configs:
#   - job_name: docker
#     pipeline_stages:
#       - docker: {}
#     static_configs:
#       - labels:
#           job: docker
#           __path__: /var/lib/docker/containers/*/*-json.log   
```
The above configuration will get us syslog's of local machine only. In order to get docker and kubernetes cluster logs we need to provide different configuration to Loki. 

### **Keepalived installation (native)**
Resource: https://docs.oracle.com/en/operating-systems/oracle-linux/6/admin/section_ksr_psb_nr.html

Keepalived is a service with High Availability and Load Balancing at one place. With the use of VIP (Virtual IP) creation it provides HA environment with nodes. If nodes are configures as Load balancers and working servers lie behind the node, then that configuration is HA and Load Balancer at one place. 

It can be installed through APT package manager. \
Run the following CLI commands:

    sudo apt-get update
    sudo apt-get install -y libipset13
    sudo apt-get install -y keepalived

Then we have to edit <a>/etc/keepalived/keepalived.conf</a> file to configure VIP. Care must be taken in assigning values to the parameters. \
**/etc/keepalived/keepalived.conf**
```
vrrp_instance VI_1 {
  state MASTER
  interface ens18
  virtual_router_id 55
  priority 150
  advert_int 1
  unicast_src_ip <MACHINE-IP-ADDR>
  unicast_peer {
    <PEER-IP-ADDR>
  }

  authentication {
    auth_type PASS
    auth_pass <SECRET-PASSWORD>
  }

  virtual_ipaddress {
    <VIP-IP-ADDR>
  }
}
```
Similar type of installation and configuration must be followed in all the nodes that are participating in HA configuration with keepalived. 

Restart the keepalived service to make the configuration changes to be effective.

    sudo systemctl enable --now keepalived.service
    sudo systemctl start keepalived.service

### **Pi-Hole Installation (Docker)**
Resource: https://github.com/pi-hole/docker-pi-hole

Pi-Hole is a DNS sinkhole. It acts as a malware/adware blocker for an entire network or a specific host. It can also be configure as a DNS server, or DHCP server. We can add custom blocklist and whitelist to block malicious sites or requests. It can be deployed in a separate machine, in a docker container, or in a Kubernetes cluster as Rancher workload.  

```bash
docker run -d \
    --name pihole \
    --restart=unless-stopped \
    --dns=127.0.0.1 \
    --dns=1.1.1.1 \
    -p 80:80 \
    -v <custom/path>:/etc/pihole \
    -v <custom/path>:/etc/dnsmasq.d \
    -e TZ="Asia/Calcutta" \
    pihole/pihole:latest
```
To change the password of Pi-Hole (docker install):
```bash
docker exec -it <pihole/container/name> bash
# inside pihole CLI
pihole -a -p
```
Pi-Hole UI can be accessed at http://localhost/. To provide
Pi-Hole services to entire network we need to point router DNS to Pi-Hole. Or we can point each individual host manually to Pi-Hole for DNS. 