Rocket.Chat [![Ansible Galaxy](https://img.shields.io/badge/galaxy-RocketChat.Server-blue.svg?style=flat)](https://galaxy.ansible.com/detail#/role/6478)
===========
Deploy [Rocket.Chat](http://rocket.chat), the ultimate open source web chat platform, with [Ansible](http://ansible.com)!

Features
--------
- __Optional full stack deployment:__  
    Fully deploy [Rocket.Chat](http://rocket.chat), including [MongoDB](http://mongodb.com) & an [Nginx](https://www.nginx.com/) reverse SSL proxy.  
    Or, deploy [Rocket.Chat](http://rocket.chat) and integrate with your existing [MongoDB](http://mongodb.com) and/or [Nginx](https://www.nginx.com/) instances/deployment methods.  

- __Optional automatic SSL cert generation:__  
    Automatically generate SSL certs for HTTPS connectivity via an [Nginx](https://www.nginx.com/) reverse proxy.  
    Or, deploy your own SSL certs!  

- __Optional automatic upgrades:__  
    If a new version of [Rocket.Chat](http://rocket.chat) is released, or if you want to follow development for testing purposes, simply update the `rocket_chat_build_version` to whichever release/branch you wish to deploy, set `rocket_chat_automatic_upgrades` to `true` and let this role do the rest!
    If there's a change to the code deployed to your [Rocket.Chat](http://rocket.chat) server (either because of a remote change on the branch you're following, or because you set a new release to fetch), this role will handle the upgrade and redeployment of the [Rocket.Chat](http://rocket.chat) service, keeping your data in tact.  
	_Note: Although this functionality has been written, there is [currently a bug in Ansible 1.8 onwards](https://github.com/ansible/ansible/issues/13182) (since fixed in 2.0) that will cause this to fail. As such, the current handler that takes care of this has been commented out, until the release of Ansible 2.0 (soon!)_

Supported Platforms
-------------------
### Debian
- Jessie (8)

### Ubuntu
- Trusty: 14.04
- Vivid: 15.04

### EL (RHEL/CentOS)
- 7

Support for other distributions/operating systems is planned for the near future.
If you'd like to see your distribution/operating system supported, please [raise an issue](https://github.com/RocketChat/Rocket.Chat.Ansible/issues)!

Role Variables
--------------
All variables have sane defaults set in [`defaults/main.yml`](defaults/main.yml)
### Defaults

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_automatic_upgrades` | false | A boolean value that determines whether or not to upgrade Rocket.Chat upon source code changes |
| `rocket_chat_upgrade_backup` | true | A boolean value that determines whether or not to back up the current Rocket.Chat version |
| `rocket_chat_upgrade_backup_path` | `"{{ rocket_chat_application_path }}"`| The path to store the back up of Rocket.Chat when `rocket_chat_upgrade_backup` is `true` |
| `rocket_chat_application_path` | `/var/lib/rocket.chat` | The destination on the filesystem to deploy Rocket.Chat to |
| `rocket_chat_version` | `master` | The version of Rocket.Chat to deploy; should be `master` for stable, or `develop` for development releases |
| `rocket_chat_tarball_remote` | See [`defaults/main.yml`](defaults/main.yml) | The remote URL to fetch the Rocket.Chat tarball from (uses `rocket_chat_version`) |
| `rocket_chat_tarball_sha256sum` | See [`defaults/main.yml`](defaults/main.yml) | The SHA256 hash sum of the Rocket.Chat tarball being fetched |
| `rocket_chat_tarball_fetch_timeout` | 100 | The time (in seconds) before the attempt to fetch the Rocket.Chat tarball fails |
| `rocket_chat_service_user` | `rocketchat` | The name of the user that will run the Rocket.Chat server process |
| `rocket_chat_service_group` | `rocketchat` | The name of the primary group for the `rocket_chat_service_user` user |
| `rocket_chat_service_host` | `"{{ ansible_fqdn }}"` | The FQDN of the Rocket.Chat system |
| `rocket_chat_service_port` | 3000 | The TCP port Rocket.Chat listens on |
| `rocket_chat_node_10_40_path` | `/usr/local/n/versions/node/0.10.40/bin` | The path to the `node` binary directory that n installs |
| `rocket_chat_original_npm` | `/usr/bin/npm` | The path to the original `npm` binary, before n installs any Node versions |
| `rocket_chat_include_mongodb` | true | A boolean value that determines whether or not to deploy MongoDB |
| `rocket_chat_mongodb_keyserver` | keyserver.ubuntu.com | The GPG key server to use when importing the MongoDB repo key |
| `rocket_chat_mongodb_gpg_key` | `7F0CEB10` | The GPG key fingerprint to import for the MongoDB repo |
| `rocket_chat_mongodb_server` | 127.0.0.1 | The IP/FQDN of the MongoDB host |
| `rocket_chat_mongodb_port` | 27017 | The TCP port to contact the MongoDB host host via |
| `rocket_chat_mongodb_packages` | `mongodb` | The name of the MongoDB package(s) to install (differs for different distros - see `vars/`) |
| `rocket_chat_mongodb_config_template` | [`mongod.conf.j2`](templates/mongod.conf.j2) | The `/etc/mongod.conf` template to deploy |
| `rocket_chat_include_nginx`| true | A boolean value that determines whether or not to deploy Nginx |
| `rocket_chat_ssl_key_path` | `/etc/nginx/rocket_chat.key` | The destination path for the Nginx SSL private key |
| `rocket_chat_ssl_cert_path` | `/etc/nginx/rocket_chat.crt` | The destination path for the Nginx SSL certificate |
| `rocket_chat_ssl_key_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_cert_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_generate_certs` | true | A boolean value that determines whether or not to generate the Nginx SSL certs |


Some variables differ between operating systems/distributions.
These are set in the `vars/` directory, typically in a file named after the distribution.

### CentOS variables
Set in [`vars/CentOS.yml`](vars/CentOS.yml)

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_dep_packages` |   - git              | A list of Rocket.Chat dependencies to install |
|                            |   - GraphicsMagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `rocket_chat_mongodb_packages` |   - mongodb        | A list of MongoDB server packages to install |
|                                |   - mongodb-server |                                              |
| `rocket_chat_mongodb_repl_lines` | `'replSet=001-rs'` | The value for the MongoDB replica set |
| `rocket_chat_mongodb_fork` |  `true` | A boolean value that sets whether or not to fork the MongoDB server process |
| `rocket_chat_mongodb_pidfile_path` | `/var/run/mongodb/mongodb.pid` | The path to the pidfile for the MongoDB server process |
| `rocket_chat_mongodb_logpath` | `/var/log/mongodb/mongod.log` | The log file path for the MongoDB server |
| `rocket_chat_mongodb_unixsocketprefix` | `/var/run/mongodb` | The path for the MongoDB UNIX socket prefix |
| `rocket_chat_mongodb_dbpath` | `/var/lib/mongodb` | The path for MongoDB to store its databases |
| `rocket_chat_nginx_process_user` | `nginx` | The user for that will be used to spawn the Nginx server process |

### CentOS 7 variables
Set in [`vars/CentOS_7.yml`](vars/CentOS_7.yml)

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_service_update_command` | `systemctl daemon-reload ; systemctl restart rocketchat` | The command to use to inform the service management system when a service manifest has changed |
| `rocket_chat_service_template` | | |
| `  src` | `rocketchat.service.j2` | The source template to deploy for the Rocket.Chat service manifest |
| `  dest` | `/usr/lib/systemd/system/rocketchat.service` | The destination to deploy the Rocket.Chat service manifest to |

### Debian variables
Set in [`vars/Debian.yml`](vars/Debian.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_dep_packages` |   - git              | A list of Rocket.Chat dependencies to install |
|                            |   - graphicsmagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `rocket_chat_mongodb_packages` | - mongodb-server | A list of MongoDB server packages to install |
|                                | - mongodb-shell  |                                              |
| `rocket_chat_mongodb_repl_lines` | `  replication:`              | The value for the MongoDB replica set |
|                                  | `    replSetName:  "001-rs"`  |                                       |
| `rocket_chat_nginx_process_user` | `www-data` | The user for that will be used to spawn the Nginx server process |

### Debian 8 variables
Set in [`vars/Debian_8.yml`](vars/Debian_8.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_service_update_command` | `systemctl daemon-reload ; systemctl restart rocketchat` | The command to use to inform the service management system when a service manifest has changed |
| `rocket_chat_service_template` | | |
| `  src` | `rocketchat.service.j2` | The source template to deploy for the Rocket.Chat service manifest |
| `  dest` | `/etc/systemd/system/rocketchat.service` | The destination to deploy the Rocket.Chat service manifest to |
| `rocket_chat_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.0 main` | The APT repository for MongoDB |

### Ubuntu variables
Set in [`vars/Ubuntu.yml`](vars/Ubuntu.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_dep_packages` |   - git              | A list of Rocket.Chat dependencies to install |
|                            |   - graphicsmagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
|                            |   - make             |                                               |
| `rocket_chat_mongodb_packages` | - mongodb-server | A list of MongoDB server packages to install |
|                                | - mongodb-shell  |                                              |
| `rocket_chat_mongodb_repl_lines` | `  replication:`              | The value for the MongoDB replica set |
|                                  | `    replSetName:  "001-rs"`  |                                       |
| `rocket_chat_nginx_process_user` | `www-data` | The user for that will be used to spawn the Nginx server process |

### Ubuntu 15 variables
Set in [`vars/Ubuntu_15.yml`](vars/Ubuntu_15.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_service_update_command` | `systemctl daemon-reload ; systemctl restart rocketchat` | The command to use to inform the service management system when a service manifest has changed |
| `rocket_chat_service_template` | | |
| `  src` | `rocketchat.service.j2` | The source template to deploy for the Rocket.Chat service manifest |
| `  dest` | `/etc/systemd/system/rocketchat.service` | The destination to deploy the Rocket.Chat service manifest to |
| `rocket_chat_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/debian wheezy/mongodb-org/3.0 main` | The APT repository for MongoDB |

### Ubuntu 14 variables
Set in [`vars/Ubuntu_14.yml`](vars/Ubuntu_14.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_service_update_command` | `initctl reload-configuration ; service rocketchat restart` | The command to use to inform the service management system when a service manifest has changed |
| `rocket_chat_service_template` | | |
| `  src` | `rocketchat_upstart.j2` | The source template to deploy for the Rocket.Chat service manifest |
| `  dest` | `/etc/init/rocketchat.conf` | The destination to deploy the Rocket.Chat service manifest to |
| `rocket_chat_mongodb_apt_repo` | `deb http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.0 multiverse` | The APT repository for MongoDB |


Install this role from Ansible Galaxy
-------------------------------------
This role is available for download from [Ansible Galaxy](http://galaxy.ansible.com).
To install this role, and track it in your Ansible code-base, use something similar to the following in your [`requirements.yml`](http://docs.ansible.com/ansible/galaxy.html#id8):

```
- src: RocketChat.Server
  path: roles/external/

```

Or, install straight from the shell, with `$ ansible-galaxy install RocketChat.Server -p path/to/ansible_roles/`  

Example Playbook
----------------

A simple playbook to run this role on all `chat_servers` systems:

    - hosts: chat_servers
      roles:
         - RocketChat.Server

A playbook to deploy Rocket.Chat to `chat_servers` but exclude the deployment of MongoDB and use an external instance. Also permit automatic upgrades of Rocket.Chat:

    - hosts: chat_servers

      vars:
        rocket_chat_automatic_upgrades: true
        rocket_chat_include_mongodb: false
        rocket_chat_mongodb_server: 10.19.3.24
  
      roles:
        - RocketChat.Server

Available tags
--------------
To run a specific set of plays, with the `--tags` flag, the available tags are:
- `vars`
- `build`
- `mongodb`
- `repo`
- `nginx`
- `upgrade`
- `service`

Management of the Rocket.Chat service
-------------------------------------
This role will deploy a service named `rocketchat`.
You can use your native service management system to start/stop/reload/restart the service.

Testing via Vagrant
-------------------
To test this role, you'll find a `Vagrantfile` and `provision.yml` playbook in the `tests/` directory.
This is, as you might have guessed, for running test deployments via [Vagrant](https://vagrantup.com).

If you'd like to test some changes, or simply see how the role works/provision a little play Rocket.Chat server locally,
you can `cd` into `tests/` and run `vagrant up` (provided you have Vagrant & VirtualBox installed).

If you take a look at the `Vagrantfile`, you'll see there's a deployment for each currently supported platform - simply comment out any you don't want to deploy (don't forget their Ansible config at the bottom, either!).  
Once deployment is finished, if you want to try Rocket.Chat out, you can visit `http://localhost:4000` in your browser (the port `4000` varies here, based on which platform you're deploying, see the `forwarded_port` value for your platform).

TODO
----
* [x] Add service user/group to run the Rocket.Chat process (for security...)
* [x] Move from PM2 to native service management systems
* [ ] Support for other OS/distros
* [ ] Use Let's Encrypt for SSL
* [ ] Offer Caddy as a webserver option

License
-------
MIT

Issues/Contributions
--------------------
Feel free to:  
[Raise an issue](https://github.com/RocketChat/Rocket.Chat.Ansible/issues)  
[Contribute](https://github.com/RocketChat/Rocket.Chat.Ansible/pulls)  
