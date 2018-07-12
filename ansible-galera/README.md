# ansible-galera

[![contributing][contributing-img]](CONTRIBUTING.md)
[![pipeline status](https://git.vpgrp.io/transverse/vpgrp-pocs/ansible-galera/badges/master/pipeline.svg)](https://git.vpgrp.io/transverse/vpgrp-pocs/ansible-galera/commits/master)

1. [Overview](#overview)
1. [Description](#description)
1. [Requirements](#requirements)
1. [Setup](#setup)
1. [Role Variables](#role-variables)
1. [Playbook Example](#playbook-example)
1. [Dependencies](#dependencies)
1. [Limitations](#limitations)
1. [Development](#development)
1. [Miscellaneous](#miscellaneous)

## Overview

[Galera Cluster](http://galeracluster.com/) for MySQL is a true Multimaster
Cluster based on synchronous replication.

## Description

A role to deploy and configure MySQL and Galera Cluster on top of it. The
replication is handled by percona `xtrabackup`.

## Requirements

None.

## Setup

Use `ansible-galaxy` or download the role in your `roles` directory.

### Initial setup

In order to initialize a new cluster, follow these steps:

- Initialize cluster on the primary node:

  ```console
  ansible-playbook -i hosts play-galera.yml --limit primary -e "galera_bootstrap=True"
  ```

- Deploy other nodes that will automatically join the cluster:

  ```console
  ansible-playbook -i hosts play-galera.yml --limit others
  ```

### Useful commands to check cluster sanity

Cluster status:

```console
MariaDB [(none)]> SHOW STATUS LIKE 'wsrep_cluster_%';
+--------------------------+--------------------------------------+
| Variable_name            | Value                                |
+--------------------------+--------------------------------------+
| wsrep_cluster_conf_id    | 2                                    |
| wsrep_cluster_size       | 2                                    |
| wsrep_cluster_state_uuid | 4def1aaa-838e-11e8-8b9e-d765e50760d1 |
| wsrep_cluster_status     | Primary                              |
+--------------------------+--------------------------------------+
4 rows in set (0.00 sec)
```

Ignored directories:

```console
MariaDB [(none)]> show global variables like 'ignore_db_dirs';
+----------------+------------+
| Variable_name  | Value      |
+----------------+------------+
| ignore_db_dirs | lost+found |
+----------------+------------+
1 row in set (0.00 sec)
```

## Role Variables

- common variables

```yaml
galera_bootstrap: false
galera_database_engine_version: 10.2
galera_database_engine: mariadb
galera_maintenance_password: {{ vault_galera_maintenance_password | mandatory() }}
galera_node_ip: 127.0.0.1
galera_root_password: {{ vault_galera_root_password | mandatory() }}

wsrep:
  - cluster_members: [127.0.0.1]
  - cluster_name: galera
  - provider_options: "base_port=4567; gcache.size=1G; gmcast.segment=0; socket.checksum=1"
  - sst_auth_pass: {{ vault_sst_auth_pass | mandatory() }}
  - sst_auth_user: {{ vault_sst_auth_user | mandatory() }}
  - sst_method: xtrabackup-v2
```

- needed secret variables format

```yaml
vault_galera_maintenance_password: "postQuantumPassword"
vault_galera_root_password: "postQuantumPassword"
vault_sst_auth_pass: "postQuantumPassword"
vault_sst_auth_user: "postQuantumPassword"
```

## Playbook Example

Use default configuration, means that nothing will be done.

```yaml
- hosts: localhost
  become: True
  roles:
    - galera
```

## Dependencies

None.

## Limitations

None.

## Development

Please read carefully CONTRIBUTING.md before making a merge request.

## Miscellaneous

```text
    ╚⊙ ⊙╝
  ╚═(███)═╝
 ╚═(███)═╝
╚═(███)═╝
 ╚═(███)═╝
  ╚═(███)═╝
   ╚═(███)═╝
```

[contributing-img]: https://img.shields.io/badge/contributing--grey.svg
