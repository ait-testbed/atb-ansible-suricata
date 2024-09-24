# Ansible Role: suricata

Ansible Role that installs an configures [suricata](https://suricata.io/)
It uses suricata-update to manage the rules

## Requirements

Debian or Ubuntu. It was tested with Ubuntu 22.04

## Defaults

```yaml
suricata_default_tpl: "default_suricata.j2"
suricata_interface: "eth0"
suricata_tpl: "suricata.yaml.j2"
```

## Role Variables

| Variable name                  | Type         | Default                                   | Description                                              |
| ------------------------------ | ------------ | ----------------------------------------- | -------------------------------------------------------- |
| suricata_tpl                   | path         | suricata.yaml.j2                          | The suricata YAMl config template to be used             |
| suricata_default_tpl           |              | default_suricata.j2                       | The suricata default file template to be used            |
| suricata_threshold_tpl         | string       | threshold.config.j2                       | The template for the threshold.config                    |
| suricata_listenmode            | string       | af-packet                                 | Run suricata in promisous or af-packet or inline         |
| suricata_interface             | list[iface]  | ["eth0"]                                  | The interface suricata should monitor                    |
| suricata_pcap_log              | bool         | true                                      | If suricata should save the packets in the pcap.log file |
| suricata_home_nets             | list[cidr]   | [192.168.0.0/16,10.0.0.0/8,172.16.0.0/12] | List of home nets for this host                          |
| suricata_external_net          | string       | !$HOME_NET                                | The external net address group                           |
| suricata_address_groups        | dict[string] | \*1                                       | Dictionary containing address group definitions          |
| suricata_port_groups           | dict[string] | \*2                                       | Dictionary containing port group definitions             |
| suricata_rule_path             | path         | /var/lib/suricata/rules                   | The path to the rules directory                          |
| suricata_rule_files            | list[string] | ["suricata.rules"]                        | The rule files suricata should use                       |
| suricata_extra_rule_files      | list[path]   | []                                        | List of additional rule files to install on the host     |
| suricata_classification_file   | path         | /etc/suricata/classification.config       | The path to the classification file                      |
| suricata_reference_config_file | path         | /etc/suricata/reference.config            | The path to the reference config file                    |
| suricata_threshold_file        | path         | /etc/suricata/threshold.config            | The path to the threshold config file                    |
| suricata_log_dir               | path         | /var/log/suricata/                        | The default suricata log directory                       |
| suricata_threshold             | list[dict]   | []                                        | The rules for the treshold.config                        |
| suricata_update_rules          | list[string] |                                           | List of suricata update rules to enable                  |
| community_id_enabled           | bool         | true                                      | If suricata should use a community id for flows          |
| community_id_seed              | int          | 0                                         | Seed for community id                                    |
| netflow_enabled                | bool         | false                                     | If suricata should generate netflow records              |

### \*1

```yaml
suricata_address_groups:
    HTTP_SERVERS: "$HOME_NET"
    SMTP_SERVERS: "$HOME_NET"
    SQL_SERVERS: "$HOME_NET"
    DNS_SERVERS: "$HOME_NET"
    TELNET_SERVERS: "$HOME_NET"
    AIM_SERVERS: "$EXTERNAL_NET"
    DNP3_SERVER: "$HOME_NET"
    DNP3_CLIENT: "$HOME_NET"
    MODBUS_CLIENT: "$HOME_NET"
    MODBUS_SERVER: "$HOME_NET"
    ENIP_CLIENT: "$HOME_NET"
    ENIP_SERVER: "$HOME_NET"
```

### \*2

```yaml
suricata_port_groups:
    HTTP_PORTS: "80"
    SHELLCODE_PORTS: "!80"
    ORACLE_PORTS: 1521
    SSH_PORTS: 22
    DNP3_PORTS: 20000
    MODBUS_PORTS: 502
```

## Use

```yaml
- hosts: localhost
  roles:
    - suricata
  vars:
    suricata_interface: "enp0s3"
    suricata_threshold:
      - { rule: 'threshold gen_id 0, sig_id 0, type threshold, track by_src, count 10, seconds 10', comment: 'Some important rule' }
      - { rule: 'suppress gen_id 1, sig_id 2009557, track by_src, ip 217.110.97.128/25' }
      - { rule: 'suppress gen_id 1, sig_id 2012086, track by_src, ip 217.110.97.128/25' }
```
