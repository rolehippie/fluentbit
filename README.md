# workspace

[![Source Code](https://img.shields.io/badge/github-source%20code-blue?logo=github&logoColor=white)](https://github.com/rolehippie/fluentbit)
[![General Workflow](https://github.com/rolehippie/fluentbit/actions/workflows/general.yml/badge.svg)](https://github.com/rolehippie/fluentbit/actions/workflows/general.yml)
[![Readme Workflow](https://github.com/rolehippie/fluentbit/actions/workflows/docs.yml/badge.svg)](https://github.com/rolehippie/fluentbit/actions/workflows/docs.yml)
[![Galaxy Workflow](https://github.com/rolehippie/fluentbit/actions/workflows/galaxy.yml/badge.svg)](https://github.com/rolehippie/fluentbit/actions/workflows/galaxy.yml)
[![License: Apache-2.0](https://img.shields.io/github/license/rolehippie/fluentbit)](https://github.com/rolehippie/fluentbit/blob/master/LICENSE)
[![Ansible Role](https://img.shields.io/badge/role-rolehippie.fluentbit-blue)](https://galaxy.ansible.com/rolehippie/fluentbit)

Ansible role to install and configure fluentbit.

## Sponsor

Building and improving this Ansible role have been sponsored by my current and previous employers like **[Cloudpunks GmbH](https://cloudpunks.de)** and **[Proact Deutschland GmbH](https://www.proact.eu)**.

## Table of content

- [Requirements](#requirements)
- [Default Variables](#default-variables)
  - [fluentbit_default_configs](#fluentbit_default_configs)
  - [fluentbit_default_filters](#fluentbit_default_filters)
  - [fluentbit_default_inputs](#fluentbit_default_inputs)
  - [fluentbit_default_outputs](#fluentbit_default_outputs)
  - [fluentbit_default_parsers](#fluentbit_default_parsers)
  - [fluentbit_extra_configs](#fluentbit_extra_configs)
  - [fluentbit_extra_filters](#fluentbit_extra_filters)
  - [fluentbit_extra_inputs](#fluentbit_extra_inputs)
  - [fluentbit_extra_outputs](#fluentbit_extra_outputs)
  - [fluentbit_extra_parsers](#fluentbit_extra_parsers)
  - [fluentbit_group_configs](#fluentbit_group_configs)
  - [fluentbit_group_filters](#fluentbit_group_filters)
  - [fluentbit_group_inputs](#fluentbit_group_inputs)
  - [fluentbit_group_outputs](#fluentbit_group_outputs)
  - [fluentbit_group_parsers](#fluentbit_group_parsers)
  - [fluentbit_host_configs](#fluentbit_host_configs)
  - [fluentbit_host_filters](#fluentbit_host_filters)
  - [fluentbit_host_inputs](#fluentbit_host_inputs)
  - [fluentbit_host_outputs](#fluentbit_host_outputs)
  - [fluentbit_host_parsers](#fluentbit_host_parsers)
  - [fluentbit_keyring](#fluentbit_keyring)
  - [fluentbit_repo_distribution](#fluentbit_repo_distribution)
  - [fluentbit_repo_release](#fluentbit_repo_release)
  - [fluentbit_service_coro_stack_size](#fluentbit_service_coro_stack_size)
  - [fluentbit_service_flush](#fluentbit_service_flush)
  - [fluentbit_service_http_listen](#fluentbit_service_http_listen)
  - [fluentbit_service_http_port](#fluentbit_service_http_port)
  - [fluentbit_service_http_server](#fluentbit_service_http_server)
  - [fluentbit_service_log_level](#fluentbit_service_log_level)
- [Discovered Tags](#discovered-tags)
- [Dependencies](#dependencies)
- [License](#license)
- [Author](#author)

---

## Requirements

- Minimum Ansible version: `2.10`

## Default Variables

### fluentbit_default_configs

List of default configs

#### Default value

```YAML
fluentbit_default_configs: []
```

#### Example usage

```YAML
fluentbit_default_configs:
  - name: example
    content: |
      [INPUT]
          Name cpu
          Tag cpu.local
          Interval_Sec 1
```

### fluentbit_default_filters

List of default filters

#### Default value

```YAML
fluentbit_default_filters:
  - name: host
    content: |
      Name modify
      Match *
      Add host {{ ansible_fqdn }}
```

#### Example usage

```YAML
fluentbit_default_filters:
  - name: parser
    content: |
      Name parser
      Match dummy.*
      Key_Name data
      Parser dummy_test
  - name: to-remove
    state: absent
```

### fluentbit_default_inputs

List of default inputs

#### Default value

```YAML
fluentbit_default_inputs:
  - name: kern
    content: |
      Name Tail
      Path /var/log/kern.log
      Path_Key file
      DB /var/lib/td-agent-bit/kern.db
      Parser syslog
  - name: auth
    content: |
      Name Tail
      Path /var/log/auth.log
      Path_Key file
      DB /var/lib/td-agent-bit/auth.db
      Parser syslog
  - name: dpkg
    content: |
      Name tail
      Path /var/log/dpkg.log
      Path_Key file
      DB /var/lib/td-agent-bit/dpkg.db
      Parser dpkg
```

#### Example usage

```YAML
fluentbit_default_inputs:
  - name: example
    content: |
      Name cpu
      Tag cpu.local
      Interval_Sec 1
  - name: to-remove
    state: absent
```

### fluentbit_default_outputs

List of default outputs

#### Default value

```YAML
fluentbit_default_outputs:
  - name: stdout
    content: |
      Name stdout
      Match *
```

#### Example usage

```YAML
fluentbit_default_outputs:
  - name: stdout
    content: |
      Name stdout
      Match *
  - name: to-remove
    state: absent
```

### fluentbit_default_parsers

List of extra parsers, similar to fluentbit_default_parsers

#### Default value

```YAML
fluentbit_default_parsers:
  - name: apache2_access
    format: regex
    regex: |
      ^(?<host>[^ ]*) [^ ]* (?<user>[^ ]*) \[(?<time>[^\]]*)\] "(?<method>\S+)(?: +(?<path>[^ ]*) +\S*)?" (?<code>[^ ]*) (?<size>[^ ]*)(?: "(?<referer>[^\"]*)" "(?<agent>.*)")?$
    time_key: time
    time_format: '%d/%b/%Y:%H:%M:%S %z'
  - name: apache2_error
    format: regex
    regex: |
      ^\[[^ ]* (?<time>[^\]]*)\] \[(?<level>[^\]]*)\](?: \[pid (?<pid>[^\]]*)\])?( \[client (?<client>[^\]]*)\])? (?<message>.*)$
    time_key: time
    time_format: '%b %d %H:%M:%S.%L %Y'
  - name: json
    format: json
    time_key: time
    time_format: '%d/%b/%Y:%H:%M:%S %z'
  - name: docker
    format: json
    time_key: time
    time_format: '%Y-%m-%dT%H:%M:%S.%L'
  - name: dockerd
    format: regex
    regex: |
      time="(?<time>[^ ]*)" level=(?<level>[^ ]*) msg="(?<msg>[^ ].*)"
    time_key: time
    time_format: '%Y-%m-%dT%H:%M:%S.%L'
  - name: syslog
    format: regex
    regex: |
      ^(?<time>[^ ]* {1,2}[^ ]* [^ ]*) (?<ident>[a-zA-Z0-9_\/\.\-]*)(?:\[(?<pid>[0-9]+)\])?(?:[^\:]*\:)? *(?<message>.*)$
    time_key: time
    time_format: '%b %d %H:%M:%S'
  - name: mongodb
    format: regex
    regex: |
      ^(?<time>[^ ]*)\s+(?<severity>\w)\s+(?<component>[^ ]+)\s+\[(?<context>[^\]]+)]\s+(?<message>.*?)$
    time_key: time
    time_format: '%Y-%m-%dT%H:%M:%S.%L'
  - name: mysql
    format: regex
    regex: |
      ^(?<time>\d{4}-\d{1,2}-\d{1,2}[ ]+\d{1,2}:\d{1,2}:\d{1,2}) (?<thread>[^ ]*) \[(?<level>[^ ]*)\] *(?<message>.*)$
    time_key: time
    time_format: '%Y-%m-%dT %H:%M:%S'
  - name: dpkg
    format: regex
    regex: |
      ^(?<time>[^ ]+ [^ ]+) (?<message>.+)$
    time_key: time
    time_format: '%Y-%m-%d %H:%M:%S'
```

### fluentbit_extra_configs

List of extra configs

#### Default value

```YAML
fluentbit_extra_configs: []
```

### fluentbit_extra_filters

List of extra filters, similar to fluentbit_default_filters

#### Default value

```YAML
fluentbit_extra_filters: []
```

### fluentbit_extra_inputs

List of extra inputs, similar to fluentbit_default_inputs

#### Default value

```YAML
fluentbit_extra_inputs: []
```

### fluentbit_extra_outputs

List of extra outputs, similar to fluentbit_default_configs

#### Default value

```YAML
fluentbit_extra_outputs: []
```

### fluentbit_extra_parsers

#### Default value

```YAML
fluentbit_extra_parsers: []
```

### fluentbit_group_configs

List of group configs

#### Default value

```YAML
fluentbit_group_configs: []
```

### fluentbit_group_filters

List of group filters, similar to fluentbit_default_filters

#### Default value

```YAML
fluentbit_group_filters: []
```

### fluentbit_group_inputs

List of group inputs, similar to fluentbit_default_inputs

#### Default value

```YAML
fluentbit_group_inputs: []
```

### fluentbit_group_outputs

List of group outputs, similar to fluentbit_default_configs

#### Default value

```YAML
fluentbit_group_outputs: []
```

### fluentbit_group_parsers

#### Default value

```YAML
fluentbit_group_parsers: []
```

### fluentbit_host_configs

List of host configs

#### Default value

```YAML
fluentbit_host_configs: []
```

### fluentbit_host_filters

List of host filters, similar to fluentbit_default_filters

#### Default value

```YAML
fluentbit_host_filters: []
```

### fluentbit_host_inputs

List of host inputs, similar to fluentbit_default_inputs

#### Default value

```YAML
fluentbit_host_inputs: []
```

### fluentbit_host_outputs

List of host outputs, similar to fluentbit_default_configs

#### Default value

```YAML
fluentbit_host_outputs: []
```

### fluentbit_host_parsers

#### Default value

```YAML
fluentbit_host_parsers: []
```

### fluentbit_keyring

Path for the repository keyring

#### Default value

```YAML
fluentbit_keyring: /usr/share/keyrings/fluentbit-archive-keyring.gpg
```

### fluentbit_repo_distribution

Enforce another distribution for the repo

#### Default value

```YAML
fluentbit_repo_distribution: '{{ ansible_distribution | lower }}'
```

### fluentbit_repo_release

Enforce another release for the repo

#### Default value

```YAML
fluentbit_repo_release: '{{ ansible_distribution_release }}'
```

### fluentbit_service_coro_stack_size

Coroutines stack size in bytes

#### Default value

```YAML
fluentbit_service_coro_stack_size: 24576
```

### fluentbit_service_flush

Interval of seconds to flush records

#### Default value

```YAML
fluentbit_service_flush: 5
```

### fluentbit_service_http_listen

Listening address for the HTTP server

#### Default value

```YAML
fluentbit_service_http_listen: 0.0.0.0
```

### fluentbit_service_http_port

Listening port for the HTTP server

#### Default value

```YAML
fluentbit_service_http_port: 2020
```

### fluentbit_service_http_server

Enable the built-in HTTP server

#### Default value

```YAML
fluentbit_service_http_server: false
```

### fluentbit_service_log_level

Set the verbosity level of the service

#### Default value

```YAML
fluentbit_service_log_level: info
```

## Discovered Tags

**_fluentbit_**

## Dependencies

- None

## License

Apache-2.0

## Author

[Thomas Boerger](https://github.com/tboerger)
