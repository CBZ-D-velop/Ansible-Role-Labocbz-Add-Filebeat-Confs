# Ansible role: labocbz.add_filebeat_confs

![Licence Status](https://img.shields.io/badge/licence-MIT-brightgreen)
![CI Status](https://img.shields.io/badge/CI-success-brightgreen)
![Testing Method](https://img.shields.io/badge/Testing%20Method-Ansible%20Molecule-blueviolet)
![Testing Driver](https://img.shields.io/badge/Testing%20Driver-docker-blueviolet)
![Language Status](https://img.shields.io/badge/language-Ansible-red)
![Compagny](https://img.shields.io/badge/Compagny-Labo--CBZ-blue)
![Author](https://img.shields.io/badge/Author-Lord%20Robin%20Crombez-blue)

## Description

![Tag: Ansible](https://img.shields.io/badge/Tech-Ansible-orange)
![Tag: Debian](https://img.shields.io/badge/Tech-Debian-orange)
![Tag: Filebeat](https://img.shields.io/badge/Tech-Filebeat-orange)

An Ansible role import / build a Filebeat configuration logfile.

The provided Ansible role serves the purpose of seamlessly integrating additional log harvesting functionality into an existing Filebeat setup. Filebeat, which should already be present, acts as a log shipper, forwarding log data to various outputs. This role streamlines the process of extending Filebeat's capabilities by creating a .yml configuration file within a designated Filebeat configuration directory.

The primary objective of this role is to enable the effortless addition of a new log harvester to Filebeat during the deployment of a specific service. This can be likened to a "hot-swappable" feature, where a new harvester can be dynamically introduced to Filebeat without disrupting its ongoing operations.

It's important to note that this role assumes that Filebeat is already up and running. The configuration settings for the associated Logstash setup are not automatically incorporated through this role; rather, it focuses solely on the Filebeat aspect. The role achieves its purpose by defining a set of input configurations within the provided YAML file. These configurations specify the type of log data to harvest, such as system logs, authentication logs, and messages, and map them to the appropriate file paths.

In essence, this Ansible role enhances operational flexibility by enabling on-the-fly expansion of Filebeat's log harvesting capabilities. By encapsulating the process of configuring additional harvesters, it simplifies and accelerates the deployment of new services while ensuring smooth data transmission from the service's log files to the designated Logstash instance.

## Folder structure

By default Ansible will look in each directory within a role for a main.yml file for relevant content (also man.yml and main):

```PYTHON
.
├── README.md  # Contains an overview of the role and its purpose.
├── defaults
│   ├── main.yml  # Contains default variables for the role that can be overridden by users.
│   └── README.md  # Contains documentation for the default variables.
├── files
│   └── README.md  # Contains documentation for the files in the directory.
├── handlers
│   ├── main.yml  # Contains handlers that can be called by tasks within the role.
│   └── README.md  # Contains documentation for the handlers.
├── meta
│   ├── main.yml  # Contains metadata about the role, including dependencies and supported platforms.
│   └── README.md  # Contains documentation for the role metadata.
├── tasks
│   ├── main.yml  # Contains tasks to be executed by the role on the managed nodes.
│   └── README.md  # Contains documentation for the tasks.
├── templates
│   └── README.md  # Contains documentation for the templates.
└── vars
    ├── main.yml  # Contains variables that are specific to the role and are not meant to be overridden.
    └── README.md  # Contains documentation for the role variables.
```

## Tests and simulations

### Basics

You have to run multiples tests. *tests with an # are mandatory*

```MARKDOWN
# lint
# syntax
# converge
# idempotence
# verify
side_effect
```

Executing theses test in this order is called a "scenario" and Molecule can handle them.

Molecule use Ansible and pre configured playbook to create containers, prepare them, converge (run the role) and verify its execution.
You can manage multiples scenario with multiples tests in order to get a 100% code coverage.

This role contains a ./tests folder. In this folder you can use the inventory or the tower folder to create a simualtion of a real inventory and a real AWX / Tower job execution.

### Command reminder

```SHELL
# Check your YAML syntax
yamllint -c ./.yamllint .

# Check your Ansible syntax and code security
ansible-lint --config=./.ansible-lint .

# Execute and test your role
molecule create
molecule list
molecule converge
molecule verify
molecule destroy

# Execute all previous task in one single command
molecule test
```

## Installation

To install this role, just copy/import this role or raw file into your fresh playbook repository or call it with the "include_role/import_role" module.

## Usage

### Vars

Some vars a required to run this role:

```YAML
---
add_filebeat_confs__path: "/etc/filebeat/conf.d"
add_filebeat_confs__group: "filebeat"

add_filebeat_confs__ignore_older: "24h"
add_filebeat_confs__close_inactive: "24h"

add_filebeat_confs__inputs:
  - name: "host-mesages"
    type: "filestream" # much better than log, see documentation
    fields:
      type: "UNIX"
      file: "host-messages"
    paths:
      - "/var/log/messages"
  #
  - name: "host-auth"
    type: "filestream"
    fields:
      type: "UNIX"
      file: "host-auth"
    paths:
      - "/var/log/auth.log"
  #
  - name: "host-syslog"
    type: "filestream"
    fields:
      type: "UNIX"
      file: "host-syslog"
    paths:
      - "/var/log/syslog"

```

The best way is to modify these vars by copy the ./default/main.yml file into the ./vars and edit with your personnals requirements.

You can set vars in the template model in Ansible AWX / Tower or just surchage them during the playbook call.

In order to surchage vars, you have multiples possibilities but for mains cases you have to put vars in your inventory and/or on your AWX / Tower interface.

```YAML
# From inventory
---
inv_prepare_host__system_users:
  - login: "filebeat"
    group: "filebeat"

inv_add_filebeat_confs__path: "/etc/filebeat/conf.d"
inv_add_filebeat_confs__group: "filebeat"

inv_add_filebeat_confs__ignore_older: "24h"
inv_add_filebeat_confs__close_inactive: "24h"

inv_add_filebeat_confs__inputs:
  - name: "host-mesages"
    type: "filestream" # much better than log, see documentation
    fields:
      type: "UNIX"
      file: "host-messages"
    paths:
      - "/var/log/messages"
  #
  - name: "host-auth"
    type: "filestream"
    fields:
      type: "UNIX"
      file: "host-auth"
    paths:
      - "/var/log/auth.log"
  #
  - name: "host-syslog"
    type: "filestream"
    fields:
      type: "UNIX"
      file: "host-syslog"
    paths:
      - "/var/log/syslog"

```

```YAML
# From AWX / Tower
---

```

### Run

To run this role, you can copy the molecule/default/converge.yml playbook and add it into your playbook:

```YAML
- name: "Include labocbz.add_filebeat_confs"
    tags:
    - "labocbz.add_filebeat_confs"
    vars:
      add_filebeat_confs__path: "{{ inv_add_filebeat_confs__path }}"
      add_filebeat_confs__group: "{{ inv_add_filebeat_confs__group }}"
      add_filebeat_confs__ignore_older: "{{ inv_add_filebeat_confs__ignore_older }}"
      add_filebeat_confs__close_inactive: "{{ inv_add_filebeat_confs__close_inactive }}"
      add_filebeat_confs__inputs: "{{ inv_add_filebeat_confs__inputs }}"
    ansible.builtin.include_role:
      name: "labocbz.add_filebeat_confs"
```

## Architectural Decisions Records

Here you can put your change to keep a trace of your work and decisions.

### 2023-08-04: First Init

* First init of this role with the bootstrap_role playbook by Lord Robin Crombez

### 2023-10-06: New CICD, new Images

* New CI/CD scenario name
* Molecule now use remote Docker image by Lord Robin Crombez
* Molecule now use custom Docker image in CI/CD by env vars
* New CICD with needs and optimization

### 2024-05-19: New CI

* Added Markdown lint to the CICD
* Rework all Docker images
* Change CICD vars convention
* New workers
* Removed all automation based on branch
* Add var__ for more visibiliy

## Authors

* Lord Robin Crombez

## Sources

* [Ansible role documentation](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html)
* [Ansible Molecule documentation](https://molecule.readthedocs.io/)
