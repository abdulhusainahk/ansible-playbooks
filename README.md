# Ansible Playbooks & Roles

[![Ansible Lint](https://github.com/abdulhusainahk/ansible-playbooks/actions/workflows/ci.yml/badge.svg)](https://github.com/abdulhusainahk/ansible-playbooks/actions/workflows/ci.yml)
[![Ansible](https://img.shields.io/badge/Ansible-%3E%3D2.9-EE0000?logo=ansible)](https://www.ansible.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Production-tested Ansible playbooks and roles for infrastructure automation — MySQL replication, Hadoop cluster provisioning, HAProxy load balancing, Docker web servers, and monitoring plugin deployment.

---

## Playbooks & Roles

| Playbook / Role | Description | Key Features |
|-----------------|-------------|--------------|
| [`ansible-role-mysql`](./ansible-role-mysql) | MySQL server installation and configuration role | Multi-OS (RedHat/Debian), root password hardening, replication support, user management |
| [`mysql_repl.yml`](./mysql_repl.yml) | MySQL master-slave replication setup | Configures master with binary logging, slave with relay, user provisioning |
| [`hadoop-cluster-automate-ansible`](./hadoop-cluster-automate-ansible) | Hadoop HDFS cluster automation | NameNode + DataNode provisioning, HDFS/core-site config templating |
| [`reverse-proxy-haproxy-ansible`](./reverse-proxy-haproxy-ansible) | HAProxy reverse proxy with backend web servers | Dynamic port config, httpd backend pool, PHP support |
| [`docker-httpd-web-server`](./docker-httpd-web-server) | Dockerized Apache web server | Docker install, image pull, container orchestration with volume mounts |
| [`httpd-configure-ansible`](./httpd-configure-ansible) | Basic httpd web server setup | Package install, content deployment, service management |
| [`httpd-service-restart-idempotent-ansible`](./httpd-service-restart-idempotent-ansible) | Idempotent httpd with handler-based restarts | Jinja2 templated config, handler-driven service restart |
| [`nagios_plugin.yml`](./nagios_plugin.yml) | Nagios monitoring plugin deployment | Plugin distribution, apt source management, scheduled updates |

---

## Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Control Node                          │
│              (Ansible Controller)                        │
│                                                          │
│  ansible-role-mysql ──► MySQL Master ◄──► MySQL Slave    │
│  hadoop-cluster     ──► NameNode + DataNodes             │
│  haproxy-ansible    ──► HAProxy ──► httpd backends       │
│  docker-httpd       ──► Docker containers                │
│  nagios_plugin      ──► Monitoring agents                │
└──────────────────────────────────────────────────────────┘
```

---

## Quick Start

### Prerequisites

- Ansible >= 2.9
- SSH access to target hosts
- Python 3 on target hosts

### Setup

```bash
# Clone the repository
git clone https://github.com/abdulhusainahk/ansible-playbooks.git
cd ansible-playbooks

# Copy and edit the inventory
cp inventory/hosts.example inventory/hosts
# Edit inventory/hosts with your actual host IPs

# Run a playbook
ansible-playbook -i inventory/hosts mysql_repl.yml
```

### MySQL Master-Slave Replication

```bash
# Configure master and slave
ansible-playbook -i inventory/hosts mysql_repl.yml
```

### HAProxy + Web Server Pool

```bash
# Deploy HAProxy load balancer with httpd backends
ansible-playbook -i inventory/hosts reverse-proxy-haproxy-ansible/haproxyplay.yml
```

### Dockerized Web Server

```bash
# Deploy Apache in Docker containers
ansible-playbook -i inventory/hosts docker-httpd-web-server/docweb.yml
```

---

## MySQL Role Details

The `ansible-role-mysql` is a full Ansible Galaxy-compatible role:

```
ansible-role-mysql/
├── defaults/main.yml      # Default variables (port, bind address)
├── handlers/main.yml      # Service restart handlers
├── tasks/main.yml         # Installation, config, replication tasks
├── templates/             # Jinja2 config templates
│   ├── my.cnf.RedHat.j2
│   ├── my.cnf.Debian.j2
│   └── mysql.cnf.j2
└── vars/                  # OS-specific variables
    ├── RedHat.yml
    └── Debian.yml
```

### Key Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `mysql_port` | `3306` | MySQL server port |
| `mysql_bind_address` | `0.0.0.0` | Bind address |
| `mysql_root_db_pass` | — | Root password (required) |
| `mysql_db` | `[]` | Databases to create |
| `mysql_users` | `[]` | Users to create with privileges |
| `mysql_repl_role` | `none` | Replication role: `master` or `slave` |
| `mysql_repl_master` | — | Master IP (when role = slave) |
| `mysql_db_id` | — | Unique server ID for replication |

---

## CI/CD

Every push and PR runs:

- **`ansible-lint`** — enforces Ansible best practices
- **`yamllint`** — validates YAML syntax

---

## License

MIT
