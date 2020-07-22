JournalMd Ansible role
=========

Installs and configures [JournalMd](https://github.com/JournalMd/).

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.

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
        - journalmd
```

License
-------

MIT
