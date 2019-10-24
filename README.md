# Iroha-Deploy Using Ansible for Multiple Network
> This role deploys multiple replicas of Iroha containers (one Iroha peer per container) on remote hosts.

## Requirements
Tested on Ubuntu 16.04, 18.04
  - Local & Remote :
    - Docker (>=17.12)
    - python3-pip (pip 19.3.1 `*python 3.6*`)
    - python-pip (pip 19.3.1 `*python 3.6*`)
    - PIP & PIP3 modules: docker(>=3.7.3), docker-compose(>=1.24.1), ansible(>=2.8)
    - git (>=2.17.1)

> Please check carefully PIP & PIP3 include module installation, because iroha-depoy script need specific python module.


## Installation
---
### Check Git & Ubuntu Version
---
**1. command :**
```BASH
admin@ubuntu:~$ git --version

git version 2.17.1
```
```BASH
admin@ubuntu:~$ lsb_release -a

No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04.3 LTS
Release:	18.04
Codename:	bionic
```
---
### Install docker and docker-compose
---
**1. Uninstall old versions :**
```BASH
admin@ubuntu:~$ sudo apt-get remove docker docker- 
engine docker.io containerd runc
```
**2. Update the `apt` package index :**
```BASH
admin@ubuntu:~$ sudo apt-get update
```
**3. Install packages to allow apt to use a repository over HTTPS :**
```BASH
admin@ubuntu:~$ sudo apt-get install \
   apt-transport-https \
   ca-certificates \
   curl \
   gnupg-agent \
   software-properties-common
```
**4. Add Docker’s official GPG key :**
```BASH
admin@ubuntu:~$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
Verify that you now have the key with the fingerprint
`9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.
```BASH
admin@ubuntu:~$ sudo apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```
**5. Add Repository :**
```BASH
admin@ubuntu:~$ sudo add-apt-repository \
   "deb [arch=amd64] 
   https://download.docker.com/linux/ubuntu \
   bionic \
   stable"
```
**6. Update the `apt` package index :**
```BASH
admin@ubuntu:~$ sudo apt-get update
```
**7. Install Docker Engine - Community and Containerd :**
```BASH
admin@ubuntu:~$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```
**8. Change the permissions of docker socket to be able to connect to the docker daemon for all user :**
```BASH
admin@ubuntu:~$ sudo chmod 777 /var/run/docker.sock
```
**9. Check Docker version :**
```BASH
admin@ubuntu:~$ docker --version

Docker version 18.06.3-ce, build d7080c1
```
**10. Test Docker run :**
```BASH
admin@ubuntu:~$ docker run hello-world
```
**11. Download the current stable release of Docker Compose :**
```BASH
admin@ubuntu:~$ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
**12. Apply executable permissions to the binary :**
```BASH
admin@ubuntu:~$ sudo chmod +x /usr/local/bin/docker-compose
admin@ubuntu:~$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```
**13. Check Docker Compose version :**
```BASH
admin@ubuntu:~$ docker-compose --version

docker-compose version 1.24.1, build 4667896b
```
---
### Install pip & pip3 //=> docker, docker-compose, ansible, other module
---
**1. Install pip & pip3 :**
```BASH
admin@ubuntu:~$ sudo apt-get install python-pip
admin@ubuntu:~$ sudo apt-get install python3-pip
```
**2. Upgrade pip3 :**
```BASH
admin@ubuntu:~$ pip3 install --upgrade pip
admin@ubuntu:~$ sudo rm /usr/bin/pip3
admin@ubuntu:~$ sudo ln ~/.local/bin/pip3 /usr/bin/pip3
```
**2. Upgrade pip :**
```BASH
admin@ubuntu:~$ pip install --upgrade pip
admin@ubuntu:~$ sudo rm /usr/bin/pip3
admin@ubuntu:~$ sudo ln ~/.local/bin/pip3 /usr/bin/pip
```
**3. Update the `apt` package index :**
```BASH
admin@ubuntu:~$ sudo apt-get update
```
**4. Check pip & pip3 version :**
```BASH
admin@ubuntu:~$  pip -V

pip 19.3.1 from /home/ax1/.local/lib/python3.6/site-packages/pip (python 3.6)
admin@ubuntu:~$  pip3 -V

pip 19.3.1 from /home/ax1/.local/lib/python3.6/site-packages/pip (python 3.6)
```
**3. Install module docker, docker-compose, jmespath, ansible :**
```BASH
admin@ubuntu:~$ sudo pip3 install docker
admin@ubuntu:~$ sudo pip3 install docker-compose
admin@ubuntu:~$ sudo pip3 install jmespath
admin@ubuntu:~$ sudo pip3 install ansible
```
**4. Make sure pip3 module version same as below :**
```BASH
admin@ubuntu:~$ pip3 list

Package                    Version
-------------------        -------------------
ansible                    2.8.6
asn1crypto                 0.24.0
attrs                      17.4.0
Automat                    0.6.0
bcrypt                     3.1.7
blinker                    1.4
cached-property            1.5.1
certifi                    2018.1.18
cffi                       1.13.1
chardet                    3.0.4
click                      6.7
cloud-init                 19.1
colorama                   0.3.7
command-not-found          0.3
configobj                  5.0.6
constantly                 15.1.0
cryptography               2.8
distro-info                0.18ubuntu0.18.04.1
docker                     3.7.3
docker-compose             1.24.1
docker-pycreds             0.4.0
dockerpty                  0.4.1
docopt                     0.6.2
httplib2                   0.9.2
hyperlink                  17.3.1
idna                       2.6
incremental                16.10.1
Jinja2                     2.10
jmespath                   0.9.4
jsonpatch                  1.16
jsonpointer                1.10
jsonschema                 2.6.0
keyring                    10.6.0
keyrings.alt               3.0
language-selector          0.1
MarkupSafe                 1.0
netifaces                  0.10.4
oauthlib                   2.0.6
PAM                        0.4.2
paramiko                   2.6.0
pip                        19.3.1
pyasn1                     0.4.2
pyasn1-modules             0.2.1
pycparser                  2.19
pycrypto                   2.6.1
pygobject                  3.26.1
PyJWT                      1.5.3
PyNaCl                     1.3.0
pyOpenSSL                  17.5.0
pyserial                   3.4
python-apt                 1.6.4
python-debian              0.1.32
pyxdg                      0.25
PyYAML                     3.12
requests                   2.18.4
requests-unixsocket        0.1.5
SecretStorage              2.3.1
service-identity           16.0.0
setuptools                 39.0.1
six                        1.11.0
ssh-import-id              5.7
systemd-python             234
texttable                  0.9.1
Twisted                    17.9.0
ufw                        0.36
unattended-upgrades        0.1
urllib3                    1.22
websocket-client           0.56.0
wheel                      0.30.0
zope.interface             4.3.2
```
---
### Install Iroha at VM HOST
---
> Link installation : https://iroha.readthedocs.io/en/latest/getting_started/index.html#prerequisites

**1. Creating a Docker Network**
```BASH
admin@ubuntu:~$ docker network create iroha-network
```
**2. Starting PostgreSQL Container**
```BASH
admin@ubuntu:~$ docker run --name some-postgres \
   -e POSTGRES_USER=postgres \
   -e POSTGRES_PASSWORD=mysecretpassword \
   -p 5432:5432 \
   --network=iroha-network \
   -d postgres:9.5
```
**3. Creating Blockstore**
```BASH
admin@ubuntu:~$ docker volume create blockstore
```
**4. Preparing the configuration files**
```BASH
admin@ubuntu:~$ git clone -b master https://github.com/hyperledger/iroha --depth=1
```
**5. Starting Iroha Container**
```BASH
admin@ubuntu:~$ docker run --name iroha \
   -d \
   -p 50051:50051 \
   -v $(pwd)/iroha/example:/opt/iroha_data \
   -v blockstore:/tmp/block_store \
   --network=iroha-network \
   -e KEY='node0' \
   hyperledger/iroha:latest
```
## Testing Iroha Ledger
---
### Create First Transaction
---
> Link installation : https://iroha.readthedocs.io/en/latest/getting_started/cli-guide.html#creating-the-first-transaction

**1. Launch iroha-cli**
```BASH
admin@ubuntu:~$ docker exec -it iroha /bin/bash
```
**2. Login default admin account**
```BASH
root@244e1831e5f8:/opt/iroha_data# iroha-cli -account_name admin@test
```
**3. New transaction (tx)**
```BASH
Choose what to do:
1. New transaction (tx)
2. New query (qry)
3. New transaction status request (st)
> : 1
```
**4. Create Asset (crt_ast)**
```BASH
Choose what to do:
14. Create Asset (crt_ast)
15. Add Peer to Iroha Network (add_peer)
16. Add Asset Quantity (add_ast_qty)
0. Back (b)
> : 14
```
**5. Set Asset name**
```BASH
Asset name: coolcoin
Domain Id: test
Asset precision: 2
```
**6. Add one more command to the transaction (add)**
```BASH
1. Add one more command to the transaction (add)
2. Send to Iroha peer (send)
3. Go back and start a new transaction (b)
4. Save as json file (save)
> : 1
```
**7. Add Asset Quantity (add_ast_qty)**
```BASH
15. Add Peer to Iroha Network (add_peer)
16. Add Asset Quantity (add_ast_qty)
0. Back (b)
> : 16
```
**8. Set Asset Quantity**
```BASH
Asset Id: coolcoin#test
Amount to add, e.g 123.456: 200.50
```
**9. Send to Iroha peer (send)**
```BASH
1. Add one more command to the transaction (add)
2. Send to Iroha peer (send)
3. Go back and start a new transaction (b)
4. Save as json file (save)
> : 2
Peer address (0.0.0.0): 
Peer port (50051):
```
## Configure Ansible for Iroha
---
### Create SSH Connection peer host to remote server
---
> Link installation : https://www.techrepublic.com/article/how-to-install-ansible-on-ubuntu-server-18-04/

**1. Generate ssh pub key at Host Server**
```BASH
admin@ubuntu:~$ ssh-keygen
```
> use blank passphrase for simple access to remote server

**2. Get pub key at Host Server**
```BASH
admin@ubuntu:~$ cat ~/.ssh/id_rsa.pub
```
**3. Put Host Server pub key to all Remote Server**
```BASH
remote1@host:~$ nano ~/.ssh/authorized_keys
```
**4. Testing connection ssh from Host > Remote Server**
```BASH
remote1@host:~$ ssh remote1@<IP_ADDRESS_REMOTE>
```
---
### Configuration ansible connection at Host Server
---
**1. Clone repositories**
```BASH
admin@ubuntu:~$ git clone https://github.com/jinrave/iroha-deploy.git
```
**2. Access folder iroha-deploy/ansible**
```BASH
admin@ubuntu:~$ cd iroha-deploy/ansible
```
**3. Config inventory/remote.list**
```BASH
remote1@host:~$ vi inventory/remote.list
```
**4. Setting remote server IP & User Access ssh**
```BASH
[all:vars]
ansible_become='yes' #access for sudo in remote server

[hosts]
10.10.1.105  ansible_user=ax1 #<IP_ADDRESS_REMOTE> ansible_user=<USER_SSH>
10.10.1.106  ansible_user=ax1 #<IP_ADDRESS_REMOTE> ansible_user=<USER_SSH>
```
> save change use `ctrl+c` and type `:wq`



## Authors
* **jinrave** - *forked from* - [iroha-deploy](https://github.com/hyperledger/iroha-deploy/tree/master/ansible/roles/iroha-docker)

## License
This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details