Ansible cryptogrot.xmr-stak role
=========

This role will install and configure the mining tool `xmr-stak` on Linux operating system. Using this role give you the possibility to configure `xmr-stak` with the needed backend from the latest **official** source code from GitHub.

Supported backend types:
- NVIDIA *(CUDA 9.1)*
- AMD/ATI *(OpenCL)*
- CPU

This role enables and configures the Linux Kernel hugepages to provide the best performances and so a better hashrate.

To increase security, a dedicated system user *(`miner`)* will be created, this user will have the Kernel hugepages configure for the `soft` and `hard` limits.

For a better redundancy, the `xmr-stak` process is handle by `systemd`. A unit will be created to run the process as a simple user and to make sure that the service is always started. We are offering two ways to manage the `xmr-stak` process *(both via `systemd`)*:
1. As daemon under the `miner` system user
2. Within a `screen` session under the `miner` system user

If you choosed to run the process within `screen`, you will have to connect as `miner` user and then attach the `screen` session.
```
$ su - miner
$ screen -ls
$ screen -x XXXX.xmr-stak
```
To detach from the `screen` session, please use `ctrl`+`a`+`d` keys combination.

With the `screen` method on RedHat family distributions some SELinux rules have to be applied to avoid a reject from SELinux complaining about `screen` and `systemd`. Don't worry, the role will apply the rules if needed.

By default we dediced to let `xmr-stak` generate the correct CPU *affinity/configuration*, of course this could be changed by editing the `cpu.txt` configuration file in `/etc/miner/` directory.

Supported algorithms:
- Cryptonigth *(the one configured by default)*
  - *(Monero, Sumokin, Electroneum, Intense, etc...)*
- Cryptonigth-ligth
  - *(AEON, etc...)*

For more information about the `xmr-stak` miner, please have a look [here](https://github.com/fireice-uk/xmr-stak "here").

Requirements
------------

Please make sure that `ansible` and `python-pip` are installed on your Linux distribution. This role has been developped using Ansible **2.4.x** version, to install the latest Ansible version please run `pip`.

```
$ sudo pip install -U ansible
```

The role configures NVIDIA and AMD backend for `xmr-stak` only, you will need to install the kernel module *(driver)* by your self, we don't want to take this responsability. Please refer to the documentation related to your Linux distribution.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

```
---
# file: roles/xmr-stak/defaults/main.yml
xmr_stak_pool_address: itns001.cryptogrot.io
xmr_stak_pool_port: 6666
xmr_stak_wallet_address: iz4gjHNRvPnhGibn7QS41rRdKYnRSD91xcecqPfR3Ti9ZKXZASusYjxi4HcafJg4Pnass13Rif52s1ySyqdQuymy2th21urXx
xmr_stak_nvidia_support: false
xmr_stak_amd_support: false
xmr_stak_cpu_support: false
xmr_stak_httpd_support: false
xmr_stak_hwloc_support: false
xmr_stak_ssl_support: true
xmr_stak_build_options: "-DHWLOC_ENABLE={{ 'ON' if xmr_stak_hwloc_support else 'OFF' }} -DCUDA_ENABLE={{ 'ON' if xmr_stak_nvidia_support else 'OFF' }} -DOpenCL_ENABLE={{ 'ON' if xmr_stak_amd_support else 'OFF' }} {{ '-DOpenCL_LIBRARY=/usr/lib64/libOpenCL.so.1' if xmr_stak_amd_support and ansible_os_family == 'RedHat' else '' }} -DCPU_ENABLE={{ 'ON' if xmr_stak_cpu_support else 'OFF' }} -DXMR-STAK_CURRENCY=ALL -DCMAKE_INSTALL_PREFIX:PATH=/usr -DMICROHTTPD_ENABLE={{ 'ON' if xmr_stak_httpd_support else 'OFF' }} -DOpenSSL_ENABLE={{ 'ON' if xmr_stak_ssl_support else 'OFF' }} {{ xmr_stak_buid_extra_options }}"
xmr_stak_buid_extra_options: ""
xmr_stak_screen: false
xmr_stak_hugepages_size: 128
```
```
---
# file: roles/xmr-stak/vars/main.yml
xmr_stak_git_url: https://github.com/fireice-uk/xmr-stak.git
xmr_stak_git_branch: master
xmr_stak_dir: /opt/xmr-stak
xmr_stak_build_dir: /opt/xmr-stak/build
xmr_stak_config_dir: /etc/miner
xmr_stak_log_dir: /var/log/miner
xmr_stak_cuda_file: https://developer.nvidia.com/compute/cuda/9.1/Prod/local_installers/cuda_9.1.85_387.26_linux
xmr_stak_wallet: iz4gjHNRvPnhGibn7QS41rRdKYnRSD91xcecqPfR3Ti9ZKXZASusYjxi4HcafJg4Pnass13Rif52s1ySyqdQuymy2th21urXx
xmr_stak_user: miner
xmr_stak_group: miner
xmr_stak_uid: 3000
xmr_stak_gid: 3000
```

```
# file: roles/xmr-stak/templates/etc/miner/config.txt.j2
{{ xmr_stak_pool_password | default('') }}
{{ xmr_stak_nicehash | default('false') }}
{{ xmr_stak_tls | default('false') }}
{{ xmr_stak_tls_fingerprint | default('') }}
{{ xmr_stak_pool_weight | default(1) }} }
{{ xmr_stak_currency | default('monero') }}
{{ xmr_stak_call_timeout | default(10) }}
{{ xmr_stak_retry_time | default(30) }}
{{ xmr_stak_giveup_limit | default(0) }}
{{ xmr_stak_debug_level | default(3) }}
{{ xmr_stak_print_motd | default('true') }}
{{ xmr_stak_h_print_time | default(60) }}
{{ xmr_stak_aes_override | default('null') }}
{{ xmr_stak_use_slow_memory | default('warn') }}
{{ xmr_stak_tls_secure_algo | default('true') }}
{{ xmr_stak_daemon_mode | default('false') }}
{{ xmr_stak_flush_stdout | default('true') }}
{{ xmr_stak_http_port | default(0) }}
{{ xmr_stak_http_user | default('') }}"
{{ xmr_stak_http_passord | default('') }}"
{{ xmr_stak_prefer_ipv4 | default('true') }}
```

When you deploy `xmr-stak` via this Ansible role, a donation to our pool will be configured *(as the same done by the `xmr-stak dev team`)* automatically. By default the miner will donate **2%** of the hashpower *(2 minutes in 100 minutes)* to our pool.
```
{{ xmr_stak_donation_value | default('2.0') }}" }
{{ xmr_stak_donation_pool | default('donate.cryptogrot.io') }}
{{ xmr_stak_donation_wallet | default(xmr_stak_wallet) }}
```
To disable the donation you will just have to configure the `xmr_stak_donation_value` variable to `0`.

There is a `xmr_stak_packages` variable in `xmr-stak/vars/` directory related to each Linux distribution supported.
- Debian
- Ubuntu
- CentOS
- Fedora
- RedHat

Dependencies
------------

The role doesn't have any dependency, you could just run `ansible-galaxy install --roles-path . cryptogrot.xmr-stak` and the role will be downloaded automatically from Ansible Galaxy.

Make it run
----------------

Few things to prepare before starting.
```
$ apt install python-pip -y
$ sudo pip install -U ansible
$ mkdir -p ~/ansible/{inventories/xmr-stak,playbooks/xmr-stak,roles}
$ cat << EOF > ~/ansible/ansible.cfg
[defaults]
host_key_checking = False
roles_path = ./roles
EOF
$ ansible-galaxy install --roles-path ~/ansible/roles cryptogrot.xmr-stak`
```

First, the Ansible inventory as to be created *`(~/ansible/inventories/xmr-stak/hosts)`*. Here we are trying to provide an example of different distributions.
```
[miner]
192.168.0.2          ansible_user=fedora ansible_python_interpreter=/usr/bin/python3
192.168.0.3          ansible_user=root
192.168.0.4          ansible_user=ubuntu
192.168.0.5          ansible_user=centos
192.168.0.[13:30] ansible_user=debian ansible_port=22
```

This command will configure `xmr-stak` to mine with CPU and connect to `itns001.cryptogrot.io` pool on port `6666` with the wallet ID `iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh`
```
$ ansible-playbook -i ~/ansible/inventories/xmr-stak/hosts ~/ansible/playbooks/xmr-stak/site.yml -e "xmr_stak_cpu_support=true xmr_stak_hwloc_support=true xmr_stak_pool_address=itns001.cryptogrot.io xmr_stak_pool_port=6666 xmr_stak_pool_wallet=iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh"
```

Tricks & Tips
----------------

It is possible to run only few parts of the role, that could be very useful if you only want to reconfigure `xmr-stak` without rebuild everything from the sources. Please find below a list of available `tags` for this roles:

- `xmr-stak`: Run all the tasks
- `xmr-stak-prepare`: Run tasks that will prepare the field
- `xmr-stak-install`: Run tasks related to the clone, donation, compilation, configuration, service
- `xmr-stak-sysctl`: Run tasks related to hugepages configuration
- `xmr-stak-pam`: Run tasks that applied `memlock` limit to the `miner` user
- `xmr-stak-selinux`: Run tasks to install SELinux module *(only for RedHat family distribution)*
- `xmr-stak-systemd`: Run tasks that will configure the `systemd` service unit
- `xmr-stak-config`: Run tasks that will configure `xmr-stak`
- `xmr-stak-clean`: Run tasks for cleaning purpose, removing build directory, cache, etc...

For example, to reconfigure `xmr-stak`, the Ansible command would be:
```
$ ansible-playbook -i ~/ansible/inventories/xmr-stak/hosts ~/ansible/playbooks/xmr-stak/site.yml -e "xmr_stak_cpu_support=true xmr_stak_hwloc_support=true xmr_stak_pool_address=itns001.cryptogrot.io xmr_stak_pool_port=6666 xmr_stak_pool_wallet=iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh -t xmr-stak-config"
```
If something changed in the `config.txt` file, the `xmr-stak` service will be restarted.

Example Playbooks
----------------

Please find below few examples of Ansible playbook to configure the `xmr-stak` miner to make it fit with your needs. The playbook should be created in `~/ansible/playbooks/xmr-stak/` directory under the `site.yml` name.

#### CPU
```
---
- name: Building xmr-stak miner for CPU usage
  hosts: miner
  user: debian
  become: true

  vars:
    - xmr_stak_cpu_support: true
    - xmr_stak_hwloc_support: true
    - xmr_stak_pool_address: itns001.cryptogrot.io
    - xmr_stak_pool_port: 6666
    - xmr_stak_aes_override: true
    - xmr_stak_wallet_address: iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh

  roles:
    - cryptogrot.xmr-stak
```

#### AMD GPU
```
---
- name: Building xmr-stak miner for AMD GPU usage
  hosts: miner
  user: debian
  become: true

  vars:
    - xmr_stak_amd_support: true
    - xmr_stak_pool_address: itns001.cryptogrot.io
    - xmr_stak_pool_port: 6666
    - xmr_stak_wallet_address: iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh

  roles:
    - cryptogrot.xmr-stak
```

#### NVIDIA GPU
```
---
- name: Building xmr-stak miner for NVIDIA GPU usage
  hosts: miner
  user: debian
  become: true

  vars:
    - xmr_stak_nvidia_support: true
    - xmr_stak_pool_address: itns001.cryptogrot.io
    - xmr_stak_pool_port: 6666
    - xmr_stak_wallet_address: iz69...ihJF5FY7ed319xS...1kXj1N7yRa...Gmwfrh

  roles:
    - cryptogrot.xmr-stak
```

License
-------

GPLv3

Author Information
------------------

cryptogrot - contact@cryptogrot.io - 2018
