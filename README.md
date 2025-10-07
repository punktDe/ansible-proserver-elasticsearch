<!-- BEGIN_ANSIBLE_DOCS -->
# ansible-proserver-elasticsearch

Elasticsearch role for Proserver

## Supported Operating Systems

- Debian 12, 13
- Ubuntu 24.04, 22.04
- FreeBSD [Proserver](https://infrastructure.punkt.de/de/produkte/proserver.html)

## Role Arguments



#### Options for `elasticsearch`

|Option|Description|Type|Required|Default|
|---|---|---|---|---|
| `use_dehydrated` | Whether to configure an SSL certificate and a reverse proxy for Elasticsearch. If set to `true`, will use ansible-proserver-nginx and ansible-proserver-dehydrated to set up an Nginx-based reverse proxy with a valid SSL certificate that listens on 0.0.0.0:443. By default, HTTPS-01 challenge will be used to create the SSL certificate. In order for this to work, `elasticsearch.domain` has to be set to a valid, DNS-resolveable FQDN (defaults to `inventory_hostname`). Please consult the documentation of [ansible-proserver-dehydrated](https://github.com/punktDe/ansible-proserver-dehydrated) for other available challenges | bool | no | False |
| `domain` | A domain that will be used for the Elasticsearch cluster name as well as reverse proxy (the latter only applies when `elasticsearch.use_dehydrated` is set) | str | no | {{ inventory_hostname }} |
| `version` | The major version of Elasticsearch to be configured/installed. If set to >=8, will require setting the password for the `elastic` superuser (`elasticsearch.users.builtin.elastic.password), since security is enabled by default starting with Elasticsearch 8.x | int | no | 8 |
| `users` | Allows for setting passwords for Elasticsearch users As of now, only supports setting the password for the `elastic` user (`elasticsearch.users.builtin.elastic.password`) | dict | yes |  |
| `prefix` | Sets folders/prefixes for Elasticsearch directories | dict of 'prefix' options | yes |  |
| `transport_security` | Settings for transport security (disabled by default) | dict of 'transport_security' options | yes |  |
| `elasticsearch.yml` | Allows to define options included in the elasticsearch.yml file Consult defaults/main.yml for options set by default, and https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html for a guide on how to configure Elasticsearch | dict | yes |  |
| `log4j2.properties` | Allows to define options included in the log4j2.properties file | dict | no |  |
| `jvm.options` | Allows to define options included in the log4j2.properties file Consult defaults/main.yml for options set by default | dict | yes |  |
| `repository` |  | dict of 'repository' options | yes |  |
| `plugins` | Will install the specified plugins using the `elasticsearch-plugin install` command | dict | no |  |
| `http_proxy` | Specifies HTTP proxy to be used for Elasticsearch commands that require Internet access (e.g. elasticsearch-plugin) | str | no |  |

#### Options for `elasticsearch.prefix`

|Option|Description|Type|Required|Default|
|---|---|---|---|---|
| `config` | Path for configuration files (e.g. elasticsearch.yml) Defaults to /etc/elasticsearch on Linux and /usr/local/etc/elasticsearch on other systems | str | yes |  |
| `bin` | Path for Elasticsearch binaries (e.g. elasticsearch-keystore) Defaults to /usr/share/elasticsearch/bin on Linux and /usr/local/lib/elasticsearch/bin on other systems | str | yes |  |
| `plugins` | Path for installing Elasticsearch plugins Defaults to /usr/share/elasticsearch/plugins on Linux and /usr/local/lib/elasticsearch/plugins on other systems | str | yes |  |

#### Options for `elasticsearch.transport_security`

|Option|Description|Type|Required|Default|
|---|---|---|---|---|
| `ca_from` | The inventory hostname of the machine which should generate the CA for transport security. Setting this variable activates transport security The resulting CA will then be copied to all the other inventory members which satisfy the following requirements \* ansible-proserver-elasticsearch role is executed on the machine \* The machine has the `elasticsearch.transport_security.ca_from variable set which is not equal to the machine's `inventory_hostname` The certificates will be generated on every machine in the cluster individually using this CA. | str | no |  |
| `cert_dns` | Domain names for which the transport security certificate will be generated Includes inventory_hostname and ansible_nodename of the current node by default | list of 'str' | no | {{ [inventory_hostname] + [ansible_nodename] }} |
| `cert_ips` | IP addresses for which the transport security certificate will be generated Includes all of the machine's IPv4 and IPv6 addresses by default | list of 'str' | no | {{ ansible_all_ipv4_addresses + ansible_all_ipv6_addresses }} |

#### Options for `elasticsearch.repository`

|Option|Description|Type|Required|Default|
|---|---|---|---|---|
| `apt` | Configuration options for APT repository used to install Elasticsearch (only applies for Debian-based systems) | dict of 'apt' options | yes |  |

#### Options for `elasticsearch.repository.apt`

|Option|Description|Type|Required|Default|
|---|---|---|---|---|
| `key_url` |  | str | no | https://artifacts.elastic.co/GPG-KEY-elasticsearch |
| `repository` |  | str | no | https://artifacts.elastic.co/packages/{{ vars.elasticsearch.version }}.x/apt |

## Dependencies
- [dehydrated](https://github.com/punktDe/ansible-proserver-dehydrated)
  - **Condition**: `elasticsearch.use_dehydrated`
- [nginx](https://github.com/punktDe/ansible-proserver-nginx)
  - **Condition**: `elasticsearch.use_dehydrated`

## Installation
Add this role to the requirements.yml of your playbook as follows:
```yaml
roles:
  - name: ansible-proserver-elasticsearch
    src: https://github.com/punktDe/ansible-proserver-elasticsearch
```

Afterwards, install the role by running `ansible-galaxy install -r requirements.yml`

## Example Playbook

```yaml
- hosts: all
  roles:
    - name: elasticsearch
```

<!-- END_ANSIBLE_DOCS -->
