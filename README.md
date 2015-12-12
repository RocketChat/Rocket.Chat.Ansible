Rocket.Chat
===========
Deploy [Rocket.Chat](http://rocket.chat)

Features
--------
- __Optional full stack deployment:__  
    Fully deploy [Rocket.Chat](http://rocket.chat), including [MongoDB](http://mongodb.com) & an [Nginx](https://www.nginx.com/) reverse SSL proxy.  
    Or, deploy [Rocket.Chat](http://rocket.chat) and integrate with your existing [MongoDB](http://mongodb.com) and/or [Nginx](https://www.nginx.com/) instances/deployment methods.  

- __Optional automatic SSL cert generation:__  
    Automatically generate SSL certs for HTTPS connectivity via an [Nginx](https://www.nginx.com/) reverse proxy.  
    Or, deploy your own SSL certs!  

- __Optional automatic upgrades:__  
    If a new version of [Rocket.Chat](http://rocket.chat) is released, or if you want to follow development for testing purposes, simply update the `rocket_chat_build_version` to whichever release/branch you wish to deploy, set `rocket_chat_automatic_upgrades` to `True` and let this role do the rest!
    If there's a change to the code deployed to your [Rocket.Chat](http://rocket.chat) server (either because of a remote change on the branch you're following, or because you set a new release to fetch), this role will handle the upgrade and redeployment of the [Rocket.Chat](http://rocket.chat) service, keeping your data in tact.  
	_Note: Although this functionality has been written, there is [currently a bug in Ansible 1.8 onwards](https://github.com/ansible/ansible/issues/13182) (since fixed in 2.0) that will cause this to fail. As such, the current handler that takes care of this has been commented out, until the release of Ansible 2.0 (soon!)_

Requirements
------------
Currently, only CentOS 7 & Ubuntu 15.04 are supported.
Support for other platforms is planned for the near future.

Role Variables
--------------
All variables have sane defaults set in [`defaults/main.yml`](defaults/main.yml)
### Defaults

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_automatic_upgrades` | False | A boolean value that determines whether or not to upgrade Rocket.Chat upon source code changes |
| `rocket_chat_application_path` | `/var/www/rocket.chat` | The destination on the filesystem to deploy Rocket.Chat to |
| `rocket_chat_version` | `master` | The version of Rocket.Chat to deploy; should be `master` for stable, or `develop` for development releases |
| `rocket_chat_tarball_remote` | See [`defaults/main.yml`](defaults/main.yml) | The remote URL to fetch the Rocket.Chat tarball from (uses `rocket_chat_version`) |
| `rocket_chat_tarball_sha256sum` | See [`defaults/main.yml`](defaults/main.yml) | The SHA256 hash sum of the Rocket.Chat tarball being fetched |
| `rocket_chat_service_host` | `"{{ ansible_fqdn }}"` | The FQDN of the Rocket.Chat system |
| `rocket_chat_service_port` | 3000 | The TCP port Rocket.Chat listens on |
| `rocket_chat_include_mongodb` | True | A boolean value that determines whether or not to deploy MongoDB |
| `rocket_chat_mongodb_server` | 127.0.0.1 | The IP/FQDN of the MongoDB host |
| `rocket_chat_mongodb_port` | 27017 | The TCP port to contact the MongoDB host host via |
| `rocket_chat_mongodb_packages` | `mongodb` | The name of the MongoDB package(s) to install (differs for different distros - see `vars/`) |
| `rocket_chat_mongodb_config_template` | [`mongod.conf.j2`](templates/ mongod.conf.j2) | The `/etc/mongod.conf` template to deploy |
| `rocket_chat_include_nginx`| True | A boolean value that determines whether or not to deploy Nginx |
| `rocket_chat_ssl_key_path` | `/etc/nginx/rocket_chat.key` | The destination path for the Nginx SSL private key |
| `rocket_chat_ssl_cert_path` | `/etc/nginx/rocket_chat.crt` | The destination path for the Nginx SSL certificate |
| `rocket_chat_ssl_key_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_cert_file` | `~` | If not using SSL cert generation, this is the path to the Nginx SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_generate_certs` | True | A boolean value that determines whether or not to generate the Nginx SSL certs |


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
| `rocket_chat_mongodb_packages` |   - mongodb        | A list of MongoDB server packages to install |
|                                |   - mongodb-server |                                              |
| `rocket_chat_mongodb_repl_lines` | `'replSet=001-rs'` | The value for the MongoDB replica set |
| `rocket_chat_mongodb_fork` |  `true` | A boolean value that sets whether or not to fork the MongoDB server process |
| `rocket_chat_mongodb_pidfile_path` | `/var/run/mongodb/mongodb.pid` | The path to the pidfile for the MongoDB server process |
| `rocket_chat_mongodb_logpath` | `/var/log/mongodb/mongod.log` | The log file path for the MongoDB server |
| `rocket_chat_mongodb_unixsocketprefix` | `/var/run/mongodb` | The path for the MongoDB UNIX socket prefix |
| `rocket_chat_mongodb_dbpath` | `/var/lib/mongodb` | The path for MongoDB to store its databases |
| `rocket_chat_nginx_process_user` | `nginx` | The user for that will be used to spawn the Nginx server process |

### Ubuntu variables
Set in [`vars/Ubuntu.yml`](vars/Ubuntu.yml)  

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_dep_packages` |   - git              | A list of Rocket.Chat dependencies to install |
|                            |   - graphicsmagick   |                                               |
|                            |   - nodejs           |                                               |
|                            |   - npm              |                                               |
| `rocket_chat_mongodb_packages` | - mongodb-org | A list of MongoDB server packages to install |
| `rocket_chat_mongodb_repl_lines` | `  replication:`              | The value for the MongoDB replica set |
|                                  | `    replSetName:  "001-rs"`  |                                       |
| `rocket_chat_nginx_process_user` | `www-data` | The user for that will be used to spawn the Nginx server process |



Example Playbook
----------------

A simple playbook to run this role on all `chat_servers` systems:

    - hosts: chat_servers
      roles:
         - RocketChat.Ansible

A playbook to deploy Rocket.Chat to `chat_servers` but exclude the deployment of MongoDB and use an external instance. Also permit automatic upgrades of Rocket.Chat:

    - hosts: chat_servers

      vars:
        rocket_chat_automatic_upgrades: True
        rocket_chat_include_mongodb: False
        rocket_chat_mongodb_server: 10.19.3.24
  
      roles:
        - RocketChat.Ansible

Available tags
--------------
To run a specific set of plays, with the `--tags` flag, the available tags are:
- `vars`
- `build`
- `mongodb`
- `repo`
- `nginx`
- `upgrade`

Management of the Rocket.Chat service
-------------------------------------

The Rocket.Chat server process is launched and managed using [PM2](https://github.com/Unitech/pm2).
Basic commands for inspecting the Rocket.Chat process are as follows:  
```
$ pm2 list                      # List all processes started with PM2
$ pm2 show rocket.chat          # Shows all information about Rocket.Chat
$ pm2 stop 0                    # Stop process with id 0
$ pm2 restart all               # Restart all apps
```

Check out the [commands overview](https://github.com/Unitech/pm2#commands-overview) for PM2 for more information.

License
-------
MIT

Issues/Contributions
--------------------
Feel free to:  
[Raise an issue](https://github.com/RocketChat/Rocket.Chat.Ansible/issues)  
[Contribute](https://github.com/RocketChat/Rocket.Chat.Ansible/pulls)  
