# setup a VM with a linux - this case use ubuntu o centos
## for ubuntu
### 1 check if ssh is installed
systemctl status ssh
#### if not installed install openssh-server
sudo apt-get update
sudo apt-get install openssh-server -y

#### check if is installed and running 
sudo systemcl status ssh
#### if not enable it and start it
sudo systemctl enable ssh --now

##### 2 Try to connect
ssh youruser@192.168.x.x

### 3. Firewall settings

Check if ufw is blocking SSH:

sudo ufw status

#### If it’s active but doesn’t show OpenSSH, allow it:

sudo ufw allow ssh
sudo ufw reload

ping 192.168.x.x
#### If ping works but SSH is refused → SSH service issue.
### If ping fails → networking issue (wrong adapter/VM IP not accessible).

### If everything is ok proceed to install docker to work with jenkins

### docker installation on ubuntu 24
# 1. Update system
sudo apt update && sudo apt upgrade -y

# 2. Remove old versions
sudo apt remove -y docker docker-engine docker.io containerd runc

# 3. Install dependencies
sudo apt install -y ca-certificates curl gnupg lsb-release

# 4. Add Docker’s official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# 5. Add Docker repository
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 6. Install Docker Engine, CLI, containerd, Buildx and Compose plugin
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 7. Verify Docker installation
docker --version
sudo docker run hello-world

# 8. (Optional) Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker
docker run hello-world

# install jenkins with docker
## to check where are the data of docker like images and the size
docker info | grep -i root
sudo dh -sh /var/lib/docker


1. Prerequisites

Make sure Docker and Docker Compose are installed (from our earlier steps). Verify:

docker --version
docker compose version

2. Create a Jenkins Docker volume

Jenkins needs persistent storage for configs, jobs, and plugins. Create volumes:

docker volume create jenkins_home

🔹 3. Run Jenkins LTS with Docker

Start Jenkins using the official jenkins/jenkins:lts image:

docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
Breakdown:
-d → run in background

--name jenkins → container name

-p 8080:8080 → Jenkins web UI on host port 8080

-p 50000:50000 → for connecting agents

-v jenkins_home:/var/jenkins_home → persistent data

4. Get the initial admin password

Check container logs:

docker logs jenkins


Look for a line like:

*************************************************************
Jenkins initial setup is required. An admin user has been created and a password generated:
<THIS_LONG_PASSWORD>
*************************************************************


Copy that password.

🔹 5. Access Jenkins

Open: http://localhost:8080

Paste the initial admin password

Install suggested plugins

Create your admin user
