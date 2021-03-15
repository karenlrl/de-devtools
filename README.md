# Data Engineering DevTools
This project's main focus is complete automation of Data Engineering (DE) tool Installation and Configuration.

Instead of spending frustrating hours on tool installation and configuration, you can clone this repos and run the attached Ansible playbook. 
This repository lets you to be five commands away from writing ELT code with a modern Data Infrastructure tool stack.

This repository leverages Ansible's Declarative Infrastructure as Code (IaC) to install and configure DE tools.

The goal of this project is to automate deployment of production-ready Data Engineering tools on any EC2 instance.

## Download and Installation
Downloading the playbook to install Data Engineering tools on your server.
```bash
git clone https://github.com/angelddaz/de-devtools ~/de-devtools
# see ./Makefile for make 
cd ~/de-devtools && make local
```
<img src="https://github.com/angelddaz/de-devtools/blob/master/images/20200406screenshot.png" width="70%" height="70%">

## Tool Focus
* Operating System: Ubuntu 18.04 
* Languages
    * Python 3
    * PostgreSQL 13
* Open Source Software
    * Airflow
    * Spark (Work in Progress)
    * Presto (Work in Progress)
    * dbt (Work in Progress)
* Cloud
    * AWS [S3, DynamoDB, Lambda] (Works in Progress)

All tools are open source or free tiers. Reference: https://free-for.dev/#/

### Software Dependencies
Ubuntu:18.04 Operating System

apt dependencies for the ansible playbook:
```bash
sudo apt-get update && sudo apt-get install -y software-properties-common git make ansible
sudo apt-add-repository --yes --update ppa:ansible/ansible
```

# Running Installed and Configured Tools

## Running Airflow

PostgreSQL Database Object Conflicts: Make sure you do not have a local Postgres Database and Roles called `airflow`.

1. Make sure your PostgreSQL Service is running
```bash
sudo service postgresql start
```

2. Build the downloaded and configured [puckel docker image](https://github.com/puckel/docker-airflow)
```bash
cd ~/de-devtools/docker-airflow
docker build .
```

3. Run a preconfigured container.
Use the LocalExecutor if you're not advanced in airflow and are using a single server or computer.
```bash
# working directory: ~/de-devtools/docker-airflow

# choose local
docker-compose -f docker-compose-LocalExecutor.yml up -d
# or choose Celery
docker-compose -f docker-compose-CeleryExecutor.yml up -d
```

4. **(Optional)** Ease of life config:
Make an alias in your `~/.bashrc` file for easier [CLI](https://airflow.apache.org/docs/stable/cli.html) usage `airflow [subcommand]`: 

* `alias airflow='docker run --rm -it puckel/docker-airflow airflow'`

## Testing
Building a docker image and running a docker container.
```bash
# Creates image
make
# run container on image
make container
```

## Code Format Standards
Following Ansible Syntax best practices with built in linter
```bash
ansible-lint main.yml
```
## Create an AWS EC2 Ubuntu instance

1. Login to AWS and Launch and instance in the EC2 section

2. Configure your VM in the following steps
    * Step 1: Select **Ubuntu Server 20.04 LTS (HVM), SSD Volume Type (64-bit x86)**
      An AMI is a template that contains the software configuration (operating system, application server,
      and applications) required to launch your instance. You can select an AMI provided by AWS, our user community,
      or the AWS Marketplace; or you can select one of your own AMIs.
    * Step 2: Next
    * Step 3: Next
    * Step 4: Next
    * Step 5: Next
    * Step 6: In the source column select the traffic that can reach your instance.
      (Rules with source of 0.0.0.0/0 allow all IP addresses to access your instance. We recommend setting security group rules to allow access from known IP addresses only)
    * Step 7: Launch
   
3. On this pop-up click the first drop down menu and select **Create a new key pair** and name your pair.
   (You have to download the private key file (*.pem file) before you can continue. Store it in a secure and accessible location. You will not be able to download the file again after it's created.)
   
4. On the next window select **View Instances** or you can go to the EC2 menu and click on the **Instances** option. There you can see the VM created.

5. Create the name "EC2_Docker" for your instance by hovering over the empty area under the **Nam**e column and pressing on the symbol that appears.

## Connect to your Linux instance depending on your OS:
   https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html

## Install Docker + Docker Compose in an AWS EC2 Instance

Run the following commands:

```

sudo apt-get update
sudo apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg-agent \
  software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
apt-cache madison docker-ce
sudo apt install docker.io
sudo apt install docker-compose

```
After running make local, run the following commands seperately
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - #
apt-cache madison docker-ce
```

## Manage Docker as a non-root user by adding user to a group.
# It may be necessary to restart the virtual machine for changes to take effect

sudo groupadd docker
sudo usermod -aG docker $ubuntu
sudo usermod -aG docker ubuntu
