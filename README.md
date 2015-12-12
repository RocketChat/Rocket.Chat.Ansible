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
Currently, only CentOS 7 is supported, but Ubuntu support will be coming very soon!

Role Variables
--------------
All variables have sane defaults set in [`defaults/main.yml`](defaults/main.yml)

|     Name     |     Default Value    |    Description     |
|---------------------------|-----------------------|------------------------------------|
| `rocket_chat_automatic_upgrades` | False | A boolean value that determines whether or not to upgrade Rocket.Chat upon source code changes |
| `rocket_chat_build_version` | v0.8.0 | The version of Rocket.Chat to build (see [Rocket.Chat's releases](https://github.com/RocketChat/Rocket.Chat/releases)) |
| `rocket_chat_service_host` | `"{{ ansible_fqdn }}"` | The FQDN of the Rocket.Chat system |
| `rocket_chat_service_port` | 3000 | The TCP port Rocket.Chat listens on |
| `rocket_chat_meteor_version` | 1.2.1 | The version of [Meteor](http://meteor.com/) to deploy Rocket.Chat with |
| `rocket_chat_meteor_platform` | os.linux.x86_64 | Platform to determine which [Meteor](http://meteor.com/) version to fetch |
| `rocket_chat_meteor_tarball_remote` | *See [`defaults/main.yml`](defaults/main.yml)* | The URL to fetch the [Meteor](http://meteor.com/) tarball from |
| `rocket_chat_include_mongodb` | True | A boolean value that determines whether or not to deploy [MongoDB](http://mongodb.com) |
| `rocket_chat_mongodb_server` | 127.0.0.1 | The IP/FQDN of the [MongoDB](http://mongodb.com) host |
| `rocket_chat_mongodb_port` | 27017 | The TCP port to contact the [MongoDB](http://mongodb.com) host host via |
| `rocket_chat_include_nginx`| True | A boolean value that determines whether or not to deploy [Nginx](https://www.nginx.com/) |
| `rocket_chat_ssl_key_path` | `/etc/nginx/rocket_chat.key` | The destination path for the [Nginx](https://www.nginx.com/) SSL private key |
| `rocket_chat_ssl_cert_path` | `/etc/nginx/rocket_chat.crt` | The destination path for the [Nginx](https://www.nginx.com/) SSL certificate |
| `rocket_chat_ssl_key_file` | `~` | If not using SSL cert generation, this is the path to the [Nginx](https://www.nginx.com/) SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_cert_file` | `~` | If not using SSL cert generation, this is the path to the [Nginx](https://www.nginx.com/) SSL private key on the Ansible control node, for deployment |
| `rocket_chat_ssl_generate_certs` | True | A boolean value that determines whether or not to generate the [Nginx](https://www.nginx.com/) SSL certs |


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
- `build`
- `mongodb`
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
