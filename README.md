indigo-dc.galaxycloud-tools
===========================

This Ansible role is for automated installation of tools from a Tool Shed into Galaxy. The role use the path scheme from the role indigo-dc.galaxycloud

When run, this role will create a virtual environment, install ephemeris and invoke the install script to tools into Galaxy. The script stop Galaxy (if running), start a local Galaxy instance on http://localhost:8080 and install tools.

The list of tools to install is provided in files/tool_list.yaml file, hosted on an external repository: https://github.com/indigo-dc/Galaxy-flavors-recipes.

Requirements
------------

This ansible role supports CentOS 7, Ubuntu 14.04 Trusty and Ubuntu 16.04 Xenial

Minimum ansible version: 2.1.2.0

Role Variables
--------------

### Path ###

``galaxy_instance_description``: set Galaxy brand

``galaxy_user``: set linux user to launch the Galaxy portal (default: ``galaxy``).

``GALAXY_UID``: set user UID (default: ``4001``).

``galaxy_FS_path``: path to install Galaxy (default: ``/home/galaxy``).

``galaxy_directory``: Galaxy directory (usually galaxy or galaxy-dist, default ``galaxy``).

``galaxy_install_path``: Galaxy installation directory (default: ``/home/galaxy/galaxy``).

``galaxy_config_path``: Galaxy config pat location.

``galaxy_config_file``: Galaxy primary configuration file.

``galaxy_venv_path``:  Galaxy virtual environment directory (usually located to ``<galaxy_install_path>/.venv``).

``galaxy_custom_config_path``: Galaxy custom configuration files path (default: ``/etc/galaxy``).

``galaxy_custom_script_path``: Galaxy custom script path (defautl: ``/usr/local/bin``).

``galaxy_log_path``: log file directory (default: ``/var/log/galaxy``).

``galaxy_instance_url``: instance url (default:  ``http://<ipv4_address>/galaxy/``).

``galaxy_instance_key_pub``: instance ssh public key to configure <galaxy_user> access.

### main options ###

``GALAXY_ADMIN_API_KEY``: Galaxy administrator API_KEY. https://wiki.galaxyproject.org/Admin/API. Please note that this key acts as an alternate means to access your account, and should be treated with the same care as your login password. To be changed by the administrator.(default value: ``GALAXY_ADMIN_API_KEY``)

``galaxy_tools_tool_list_files``: a list of yml files that list the tools to be installed.

``galaxy_tools_base_dir``: base dir to install installation script (default: ``/tmp``).

``galaxy_flavor``: galaxy flavor to install. Each flavor corresponds to a directory hosted here: https://github.com/indigo-dc/Galaxy-flavors-recipes (defautl: ``galaxy-no-tools``).

``lock_file_path``: add lock file to avoid role re-run during recipe update. To re-run the role remove the lock file {{lock_file_path}}/indigo-dc.galaxycloud-tools.lock (default: ``/var/run``).

``install_workflows: install workflows (default: ``false``).

``install_data_libraries: install data libs (default: ``false``).

``install_interactive_tours: enable interactive tours installation (default: ``false``).

``export_dir``: Galaxy userdata are stored here (default: ``/export``).

This role exploits a lite version of galaxy to install data-libraries. It install dataset in /home/galaxy/galaxy/database/files/000 by defaults. 
If the default galaxy database directory is different you have two options. By default the role read galaxy.ini and move datasets to file_path dir:
```yaml
     move_datasets: true
     set_dataset_dest_dir: false
```

Otherwise you can set the destination directory. Dataset_dest_dir must exist, since the role will not create it.
```yaml
     move_datasets: true
     set_dataset_dest_dir: true
     dataset_dest_dir: '/path/to/dir'
```
Defaults values:

``move_datasets``:``true``

``set_dataset_dest_dir``:``true``

``dataset_dest_dir``:``/path/to/dir``

``add_more_assets``: add custom resources (i.e. visualisations plugins, custom web pages, etc.). Since there is no a standard way to retrieve and install visualisation plugin, we keep this recipes external and implement a common interface to insall these resources (default: ``false``).

Create bootstrap user

if an apy key is not present on galaxy, a new user is created to istall tools and removed.
This is a very basic implementation. Advanced one is located here: https://raw.githubusercontent.com/indigo-dc/ansible-galaxy-tools/master/files/manage_bootstrap_user.py
Currently, to create it, few informations are needed:
- galaxy installation path
- galaxy_database_connection
- and pbkdf2 enabled

``create_bootstrap_user``: ``false``

``galaxy_database_connection``: ``postgresql://galaxy:galaxy@localhost:5432/galaxy``

``use_pbkdf2``: ``true``

``bootstrap_user_mail``: ``admin@server.com``

``bootstrap_user_name``: ``admin``

``bootstrap_user_password``: ``password``

By default, the api key is random-generated, overwriting the ``galaxy_admin_api_key`` variable assignment. You can set it to a defined value, by setting this ``create_random_api_key`` to false.
``create_random_api_key``: ``true``

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
    - hosts: server
      roles:
        - role: indigo-dc.galaxycloud-tools
          galaxy_flavor: "galaxy-rna-workbench"
          galaxy_admin_api_key: "ADMIN_API_KEY"
          when: galaxy_flavor != "galaxy-no-tools"
```

License
-------

Apache Licence v2
