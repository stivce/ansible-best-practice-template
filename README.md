# Layout
The following section shows one of many possible ways to organize playbook content.

Your usage of Ansible should fit your needs, however, not ours, so feel free to modify this approach and organize as you see fit.

One crucial way to organize your playbook content is Ansible’s “roles” organization feature, which is documented as part of the main playbooks page. You should take the time to read and understand the roles documentation which is available here: Roles.

## The top level of the directory would contain files and directories like so:

```
production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1.yml             # here we assign variables to particular groups
   group2.yml
host_vars/
   hostname1.yml          # here we assign variables to particular systems
   hostname2.yml

library/                  # if any custom modules, put them here (optional)
module_utils/             # if any custom module_utils to support modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""

```

### file: production
```
[main_production_machine]
[HOSTNAME-HERE] ansible_host=[IP-ADDRESS-HERE] ansible_user=[USER] ansible_connection=ssh ansible_ssh_private_key_file=/[USER]/.ssh/id_rsa ansible_python_interpreter=/usr/bin/python3
```
### file: site.yml
```
---
- name: apply common configuration to all nodes
  hosts: [HOSTNAME-HERE]
  gather_facts: false
  become: yes
  ignore_errors: yes
  roles:
         - essential-setup
         - directory-setup
```
### file: ansible.cfg
```
[defaults]
INVENTORY = production
interpreter_python=auto_silent  

[ssh_connections]
pipelining = true
```
### file: group_vars/all
```
---
ntp: ntp-atlanta.example.com
backup: backup-atlanta.example.com
```

### file: host_vars/db-bos-1.example.com
```
---
foo_agent_port: 86
bar_agent_port: 99
```


