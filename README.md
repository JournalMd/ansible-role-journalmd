JournalMd Ansible role
=========

Installs and configures [JournalMd](https://github.com/JournalMd/).

Role Variables
--------------

```yaml
################################################################################
# General
################################################################################
# instance/service name (journalmd, journalmd-test, ...)
journalmd_name: journalmd

# web server domain
journalmd_web_server_name: 192.168.10.10 # my.journal-md.com

################################################################################
# ClientWeb
################################################################################
# install clientweb?
journalmd_clientweb_install: yes

# clientweb root dir
journalmd_clientweb_dir: /var/www/journalmd-clientweb

# API url
journalmd_clientweb_api_location: http://{{journalmd_web_server_name}}:8500/api/

################################################################################
# Server
################################################################################
# install server?
journalmd_server_install: yes

# server root dir
journalmd_server_dir: /var/www/journalmd-server

# secret for encryption
journalmd_server_secret: CHANGEME

# ASPNETCORE_URLS must be used in the nginx config!
journalmd_server_aspnetcore_urls: https://localhost:5001;http://localhost:5000

# mysql server or hostname
journalmd_mysql_server: 127.0.0.1
```

Example dependencies config

```yaml
# geerlingguy.mysql
mysql_port: "3306"
mysql_root_username: root
mysql_root_password_update: no
mysql_root_password: CHANGEMEjmdtest
mysql_databases:
  - name: journalmd
    encoding: utf8mb4
    collation: utf8mb4_unicode_ci
mysql_users:
  - name: journalmd
    host: "%"
    password: CHANGEMEjmdtest
    priv: "journalmd.*:ALL"

# geerlingguy.nodejs
nodejs_version: "12.x"
nodejs_npm_global_packages:
    - name: vue-cli
    - name: tsc

# ocha.yarn

# ocha.dotnet-core
dotnet_package: "dotnet-sdk-3.1"

# geerlingguy.nginx
nginx_conf_template: "{{ playbook_dir }}/roles/journalmd/templates/nginx.conf.j2"
nginx_sendfile: "off" # VAGRANT BUG!!!!!!
nginx_remove_default_vhost: true
nginx_server_tokens: "off"
nginx_vhosts:
  - listen: "80"
    server_name: "{{ journalmd_web_server_name }}"
    filename: "journalmd_clientweb.80.conf"
    extra_parameters: |
      location / {
        root   "{{journalmd_clientweb_dir}}/dist";
        index  index.html;
        try_files $uri $uri/ /index.html;
      }
      error_page   500 502 503 504  /50x.html;
      location = /50x.html {
        root   /usr/share/nginx/html;
      }
  - listen: "8500"
    server_name: "{{ journalmd_web_server_name }}"
    filename: "journalmd_server.8500.conf"
    extra_parameters: |
      location / {
          proxy_pass         http://localhost:5000;
          proxy_http_version 1.1;
          proxy_set_header   Upgrade $http_upgrade;
          proxy_set_header   Connection keep-alive;
          proxy_set_header   Host $host;
          proxy_cache_bypass $http_upgrade;
          proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
          proxy_set_header   X-Forwarded-Proto $scheme;
      }
```

Dependencies
------------

- mysql 5+
- nodejs 12+
- nginx
- yarn
- dotnet-core 3.x

Example Playbook
----------------

[JournalMdInfrastructure](https://github.com/JournalMd/JournalMdInfrastructure)

License
-------

MIT

ToDo
----

- Improve secret handling for server
