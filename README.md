JournalMd Ansible role
=========

Installs and configures [JournalMd](https://github.com/JournalMd/).

Role Variables
--------------

```yaml
x
```

Dependencies config

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
```

Dependencies
------------

- geerlingguy.mysql
- geerlingguy.nodejs
- geerlingguy.nginx
- ocha.yarn
- ocha.dotnet-core

Example Playbook
----------------

```yaml
    - name: JournalMd installation
      hosts: servers
      become: true
      roles:
        - ansible-role-journalmd
```

The role name can be changed with named import in `requirements.yml`. 

```
- name: "journalmd"
  src: "https://github.com/JournalMd/ansible-role-journalmd.git"
```

License
-------

MIT
