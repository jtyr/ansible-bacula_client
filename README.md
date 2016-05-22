bacula_client
=============

Ansible role which helps to install and configure Bacula Client.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

# Example of how to use the role with default parameters
- hosts: myhost1
  roles:
    - bacula_client

# Example of how to specify the client name
- hosts: myhost2
  vars:
    # This is the custom FD name
    bacula_client_config_filedaemon_options_name: localhost.localdomain-fd
  roles:
    - bacula_client
```

Please refer to the
[`bacula_dir`](https://github.com/jtyr/ansible-bacula_director) for more
complete example of Bacula installation.

This role requires [Config
Encoders](https://github.com/jtyr/ansible/blob/jtyr-config_encoders/lib/ansible/plugins/filter/config_encoders.py).
Download the file and put it into the `filter_plugins` directory in the root of
your playbook:

```
$ mkdir ./filter_plugins
$ cd ./filter_plugins
$ curl -O https://github.com/jtyr/ansible/blob/jtyr-config_encoders/lib/ansible/plugins/filter/config_encoders.py
```


Role variables
--------------

Variables used by the role is as follows:

```
# Service name
bacula_client_service: bacula-fd

# Package to be installed (exact version can be specified here)
bacula_client_pkg: bacula-client

# Path the to the Client config file
bacula_client_config_file_path: /etc/bacula/bacula-fd.conf


# Values of the default FileDaemon options
bacula_client_config_filedaemon_options_name: "{{ inventory_hostname }}-fd"
bacula_client_config_filedaemon_options_fdport: 9102
bacula_client_config_filedaemon_options_working_directory: /var/spool/bacula
bacula_client_config_filedaemon_options_pid_directory: /var/run
bacula_client_config_filedaemon_options_pid_max_jobs: 20

# Default FileDaemon resource options for the first FileDaemon definition
bacula_client_config_filedaemon_options__default:
  - Name = {{ bacula_client_config_filedaemon_options_name }}
  - FD Port = {{ bacula_client_config_filedaemon_options_fdport }}
  - Working Directory = {{ bacula_client_config_filedaemon_options_working_directory }}
  - Pid Directory = {{ bacula_client_config_filedaemon_options_pid_directory }}
  - Maximum Concurrent Jobs = {{ bacula_client_config_filedaemon_options_pid_max_jobs }}

# Custom FileDaemon resource options
bacula_client_config_filedaemon_options__custom: []

# Default FileDaemon resource options
bacula_client_config_filedaemon:
  - FileDaemon: "{{
      bacula_client_config_filedaemon_options__default +
      bacula_client_config_filedaemon_options__custom }}"


# Default values of Director resource options
bacula_client_config_director_options_name: bacula-dir
bacula_client_config_director_options_password: bacula

# Default Director resource options
bacula_client_config_director_options__default:
  - Name = {{ bacula_client_config_director_options_name }}
  - Password = {{ bacula_client_config_director_options_password }}

# Custom Director resource options
bacula_client_config_director_options__custom: []

# First Director resource
bacula_client_config_director__default:
  - Director: "{{
      bacula_client_config_director_options__default +
      bacula_client_config_director_options__custom }}"

# Variable for additional Director resources (e.g. for monitor)
bacula_client_config_director__custom: []

# Final Director resource
bacula_client_config_director: "{{
  bacula_client_config_director__default +
  bacula_client_config_director__custom }}"


# Default values of Messages resource options
bacula_client_config_messages_options_name: Standard
bacula_client_config_messages_options_director_name: bacula-dir
bacula_client_config_messages_options_director: "{{ bacula_client_config_messages_options_director_name }} = all, !skipped, !restored"

# Default Messages resource options
bacula_client_config_messages_options__default:
  - Name = {{ bacula_client_config_messages_options_name }}
  - Director = {{ bacula_client_config_messages_options_director }}

# Custom Messages resource options
bacula_client_config_messages_options__custom: []

# First Messages resource
bacula_client_config_messages__default:
  - Messages: "{{
      bacula_client_config_messages_options__default +
      bacula_client_config_messages_options__custom }}"

# Variable for additional Messages resources
bacula_client_config_messages__custom: []

# Final Messages resource
bacula_client_config_messages: "{{
  bacula_client_config_messages__default +
  bacula_client_config_messages__custom }}"


# Custom resources
bacula_client_config_custom: []


# Path to the sysconfig file
bacula_client_sysconfig_file: /etc/sysconfig/bacula-fd

# Default values of the default sysconfig options
bacula_client_sysconfig_user: ""
bacula_client_sysconfig_group: ""

# Default sysconfig options
bacula_client_sysconfig__default:
  FD_USER: "{{ bacula_client_sysconfig_user }}"
  FD_GROUP: "{{ bacula_client_sysconfig_group }}"

# Custom sysconfig options
bacula_client_sysconfig__custom: {}

# Final sysconfig
bacula_client_sysconfig: "{{
  bacula_client_sysconfig__default.update(bacula_client_sysconfig__custom) }}{{
  bacula_client_sysconfig__default }}"
```


Dependencies
------------

- [Config Encoders](https://github.com/jtyr/ansible/blob/jtyr-config_encoders/lib/ansible/plugins/filter/config_encoders.py)
- [`bacula_console`](https://github.com/jtyr/ansible-bacula_console) (optional)
- [`bacula_director`](https://github.com/jtyr/ansible-bacula_director) (optional)
- [`bacula_storage`](https://github.com/jtyr/ansible-bacula_client) (optional)


License
-------

MIT


Author
------

Jiri Tyr
