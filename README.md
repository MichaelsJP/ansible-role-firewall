MichaelsJP ansible-role-firewall that works with docker.
=========
![Build](https://github.com/MichaelsJP/ansible-role-firewall/workflows/Ansible%20Role%20Firewall%20Build/badge.svg)


Introduction
------------
**Important:**

- Only used to provision ubuntu and debian for now.
- **SSH** make sure to set `bootstrap_firewall_restrict_to_internal`, if you have no ability to access the server via
  internal interfaces. Else you will lose access via ssh.

- This role configures linux `IPtables`. The result `works properly with Docker` if needed and prevents it from being
  accessible from the webs.
- The base setup assumes a firewall in front of the server, so connections are only accepted if they're not coming
  via `eth0`.
- Outgoing system traffic that originates from the server itself is allowed for now to provide the ability for system
  updates etc. since configuring routed internet access via another interface is out of scope for a simple and generic
  ansible role.

Important results with a standard run:

```text
- Docker is secured by iptables and the application ports also apply to it respectively.
- The default network interface is `eth0`.
- External SSH access is disabled. Only access via other networks other than `eth0` is allowed.
- External HTTP/HTTPS/HTTP-ALT (80,443,8080) access is disabled. Only access via other networks other than `eth0` is allowed.
- IPtables integrates with the bootstrap role and logs are written into `ver/log/iptables.log`

```

Requirements
------------
This role should be used with:

```text
- https://github.com/MichaelsJP/ansible-role-bootstrap.git
```

Role Variables
--------------
Below are the available variables together with their default values:

```yaml
# Interface name that connects the server to the webs.
bootstrap_firewall_interface_name: eth0 # Default: eth0

# Default 80, 443, 8080. Multiple ports with , separated.
bootstrap_firewall_tcp_ports: [ ]

# Default no ports. Multiple ports with , separated.
bootstrap_firewall_udp_ports: [ ]

# Decide if docker should be handled by the iptable rules defined by this role. (Heavily recommended!)
# Else docker related connections will be open to the public.
bootstrap_firewall_docker_secure: true # Default: true

# Decide if iptables will turn of connections from external sources.
bootstrap_firewall_restrict_to_internal: true # Default is true.

# Restrict ssh access to specific ips and networks. Multiple need to be separated with commas. e.g. -s 0.0.0.0,127.0.0.1/8.
# Even when `bootstrap_firewall_restrict_to_internal` is set to true, it will still accept connections from external networks.
bootstrap_firewall_restricted_ssh_access_ip: [ ]
```

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for
users too:

```yaml
    - hosts: servers
      roles:
        - src: git+https://github.com/MichaelsJP/ansible-role-bootstrap.git
          version: v0.1
        - src: git+https://github.com/MichaelsJP/ansible-role-firewall.git
          version: v0.1
```

License
-------

Apache-2.0

Author Information
------------------

[Julian Psotta](https://github.com/MichaelsJP)
