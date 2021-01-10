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
bootstrap_firewall_interface_name: "eth0" # Default: eth0

# Define internal tcp ports. Multiple ports with , separated. e.g. '80,443,8080'.
bootstrap_firewall_tcp_ports_internal: "80,443,8080"

# Define internal udp ports. Multiple ports with , separated. e.g. '80,443,8080'.
bootstrap_firewall_udp_ports_internal: [ ]

# Define external tcp ports. Multiple ports with , separated. e.g. '80,443,8080'.
bootstrap_firewall_tcp_ports_external: [ ]

# Define external udp ports. Multiple ports with , separated. e.g. '80,443,8080'.
bootstrap_firewall_udp_ports_external: [ ]

# Define allowed outgoing tcp ports. Multiple ports with , separated. e.g. '80,443,8080'.
bootstrap_firewall_tcp_ports_outgoing: "21,22,25,43,80,443,465,587,995" # Allow on all interfaces. Default: (SSH,FTP,STMP,WHOIS,HTTP,HTTPS,SMTPS,SUBMISSION,POP3S)

# Define allowed outgoing udp ports. Multiple ports with , separated. e.g. '80,443,8080'.
# DNS and ntp are needed for the system to work properly.
bootstrap_firewall_udp_ports_outgoing: "53,123" # Allow on all interfaces. Default: (dns, ntp)

# Decide if iptables will turn of connections from external sources.
bootstrap_firewall_restrict_incoming_icmp_to_internal: true # When icmp is reachable and not dropped, the outside knows the server is not reachable but online and botnets will continue hammering it.

# Decide if docker should be handled by the iptable rules defined by this role. (Heavily recommended!)
# Else docker related connections will be open to the public completely.
bootstrap_firewall_docker_secure: true

# Allow all access to specific ips and networks.
# This is especially handy if an external backup routine like borg needs SSH access from a static ip.
# SSH is always denied from external besides the ips or ip ranges defined below.
# Only enable when necessary! Activating it will put pressure on the cpu, when checking ssh requests against ip addresses.
bootstrap_firewall_restricted_access_ip: [ ] # Multiple need to be separated with commas. e.g. 0.0.0.0,127.0.0.1/8.

```

Example Playbook
----------------

```yaml
    - hosts: servers
      roles:
        - { role: michaelsjp.ansible_role_bootstrap }
        - { role: michaelsjp.ansible-role-firewall }
```

License
-------

Apache-2.0

Author Information
------------------

[Julian Psotta](https://github.com/MichaelsJP)
