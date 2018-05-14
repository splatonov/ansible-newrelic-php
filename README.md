NewRelic agent
===

This role installs and configures the newrelic php agent.

[PHP agent installation: Ubuntu and Debian](https://docs.newrelic.com/docs/agents/php-agent/installation/php-agent-installation-ubuntu-debian)

# Tips

* Supported PHP versions: 5.3, 5.4, 5.5, 5.6, 7.0, 7.1, 7.2
* Don't afraid about newrelic-php5 package, it compatible with any php
* You should restart php daemons manually (apache2, php-fpm etc) to apply any newrelic agent configuration changes

Role Variables
--------------

## newrelic_appname

It would be used at https://rpm.newrelic.com to differentiate your applications.

You also can specify [multiple appnames on single host](https://docs.newrelic.com/docs/agents/php-agent/configuration/name-your-php-application)

## newrelic_license

Required variable. It wouldn't work without license key

You can get in in your newrelic account settings

## newrelic_daemon_port

Default value: /tmp/.newrelic.sock

You can set this to an iteger value, then daemon will listen on a TCP socket

## newrelic_high_security

Enables the *high security* mode on agent side.

NewRelic website information
```
New Relic's default agent settings provide a high level of security. However, you may need to guarantee that even if the default agent settings are overridden to be more permissive, no sensitive data will ever be sent to New Relic. If this is the case, then you will want to turn on high security.
```

[More information](https://docs.newrelic.com/docs/agents/manage-apm-agents/configuration/high-security-mode)

## newrelic_process_host_display_name

Display name for server in https://rpm.newrelic.com

It will be server hostname by default

## newrelic_browser_monitoring_auto_instrument

By default NewRelic agents inject the JavaScript to header and footer in HTML to collect end user data. Set newrelic_browser_monitoring_auto_instrument to false if you want to disable it

## newrelic_daemon_auditlog

If you want to see what sends to NewRelic collector - specify path

```
newrelic_daemon_auditlog: "/var/log/newrelic/audit.log"
```

## newrelic_php_folders:

You can specify the php configuration folders where newrelic.ini will be placed

By default in will be placed in any available php configuration folder ;)

# Other variables

* newrelic-php variables described at [newrelic.ini template](templates/newrelic.ini.j2)
* newrelic-daemon variables described at [newrelic.cfg template](templates/newrelic.cfg.j2)

Examples
========

# Basic 
```
---
- name: newrelic
  hosts: backends
  roles:
  - role: newrelic
    newrelic_appname: "My Best PHP Service"
    newrelic_license: "abababababbabababba"
    when: ansible_os_family == 'Debian'
````

# Advanced
```
---
- name: newrelic
  hosts: backends
  roles:
  - role: newrelic
    newrelic_appname: "My Best PHP Service"
    newrelic_license: "abababababbabababba"
    newrelic_daemon_port: "10000"
    newrelic_process_host_display_name: "{{ inventory_hostname }}"
    newrelic_php_folders:
    - /etc/php5
    - /etc/php/7.0
    when: ansible_os_family == 'Debian'
```

# Paranoid

```
---
- name: newrelic
  hosts: backends
  roles:
  - role: newrelic
    include_vars: "encrypted_vault_where_newrelic_license_specified.yml"
    newrelic_appname: "My Best PHP Service"
    newrelic_daemon_port: "10000"
    newrelic_process_host_display_name: "{{ inventory_hostname }}"
    newrelic_high_security: "true"
    newrelic_daemon_auditlog: "/var/log/newrelic/audit.log"
    newrelic_browser_monitoring_auto_instrument: "false"
    newrelic_php_folders:
    - /etc/php5
    - /etc/php/7.0
    when: ansible_os_family == 'Debian'
```


Usage
========

### Installation 
```
$ ansible-playbook newrelic.yml
```

### Reconfigure
```
$ ansible-playbook newrelic.yml -t config
```
