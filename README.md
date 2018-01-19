# Ansible cryptogrot.xmr-stak role

This role will install and configure the mining tool `xmr-stak` on Linux operating system. Using this role give you the possibility to configure `xmr-stak` with the needed backend from the latest **official** source code from GitHub.

## Overview

* [Features](#features)
* [Quickstart](#quickstart)
  - [Requirements](#requirements)
  - [CPU backend](#cpu)
  - [NVIDIA backend](#nvidia) *(experimental)*
  - [AMD/ATI backend](#amd/ati) *(experimental)*
* [Advanced setup and configuration](doc/ADVANCED.md)

## Features

Supported backend types:
- NVIDIA *(CUDA 9.1)*
- AMD/ATI *(OpenCL)*
- CPU

Supported algorithms:
- Cryptonigth *(the one configured by default)*
  - *(Monero, Sumokin, Electroneum, Intense, etc...)*
- Cryptonigth-ligth
  - *(AEON, etc...)*

## Quickstart

### Requirements

Ubuntu/Debian
```
$ sudo apt install python-pip -y
```
Fedora
```
$ sudo dnf install python-pip -y
```
CentOS 7
```
$ sudo yum install python-pip -y
```

```
$ sudo pip install -U pip ansible
$ mkdir -p ~/ansible/{inventories/xmr-stak,playbooks/xmr-stak,roles}
$ cat << EOF > ~/ansible/ansible.cfg
[defaults]
host_key_checking = False
roles_path = ./roles
EOF
$ ansible-galaxy install --force --roles-path ~/ansible/roles cryptogrot.xmr-stak
```

The Ansible inventory as to be created in `~/ansible/inventories/xmr-stak/hosts` file. Here we are trying to provide an example for different distributions.
```
[miner]
192.168.0.2          ansible_user=fedora ansible_python_interpreter=/usr/bin/python3
192.168.0.3          ansible_user=fedora
192.168.0.4          ansible_user=root
192.168.0.5          ansible_user=ubuntu
192.168.0.6          ansible_user=centos
192.168.0.[13:30] ansible_user=debian ansible_port=22
```

### CPU

```
$ ansible-playbook -i ~/ansible/inventories/xmr-stak/hosts \
    ~/ansible/playbooks/xmr-stak/site.yml \
	-e "xmr_stak_cpu_support=true \
	xmr_stak_hwloc_support=true \
	xmr_stak_pool_address=itns001.cryptogrot.io \
	xmr_stak_pool_port=6666 \
	xmr_stak_wallet_address=iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh"
```

### NVIDIA

```
$ ansible-playbook -i ~/ansible/inventories/xmr-stak/hosts \
    ~/ansible/playbooks/xmr-stak/site.yml \
	-e "xmr_stak_nvidia_support: true \
	xmr_stak_pool_address=itns001.cryptogrot.io \
	xmr_stak_pool_port=6666 \
	xmr_stak_wallet_address=iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh"
```

### AMD/ATI

```
$ ansible-playbook -i ~/ansible/inventories/xmr-stak/hosts \
    ~/ansible/playbooks/xmr-stak/site.yml \
	-e "xmr_stak_amd_support: true \
	xmr_stak_pool_address=itns001.cryptogrot.io \
	xmr_stak_pool_port=6666 \
	xmr_stak_wallet_address=iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh"
```

## License

GPLv3

## Author Information

cryptogrot - contact@cryptogrot.io - 2018
