---
title: "rhadmin"
weight: 40
---

This section explains the `rhadmin` commands  used to control the AdminService.

### Commands

The following table describes the `rhadmin` commands.

| **Command**      | **Argument**                             | **Description**                                                                                                |
| :--------------- | :--------------------------------------- |:-------------------------------------------------------------------------------------------------------------- |
| `getconfig`      | `<service name>`                         | Displays the current configuration values for the listed service. Multiple arguments can be specified.             |
| `list`           |                                          | Shows the current list of configured services.                                                                   |
| `query`          | `all`, `<domain name>`, `<service name>`| Query a service for extended status. `all` queries all running services, `<domain name>` queries all services in the specified domain, `<service name>` queries a specific service. Can specify multiple `<domain name>` or `<service name>` arguments. |
| `query <type>`   | `all`, `<domain name>`                   | Query a `<type`> of service (`domain`, `nodes`, or `waveforms`). `all` queries all domains, `<domain name>` queries the specified. domain. |
| `reload`         |                                          | AdminService will restart itself and reread all configuration files. Running services are not affected.  |
| `restart`        | `all`, `<domain name>`, `<service name>` | Restarts a service. `all` restarts all running services,  `<domain name>` restarts all running services in the specified domain, `<service name>` restarts a specific service.  Can specify multiple `<domain name>` or `<service name>` arguments.   |
| `restart <type>` | `all`, `<domain name>`                   | Restarts a `<type>` of service (`domain`, `nodes`, or `waveforms`). `all` restarts all domains, `<domain name>` restarts the specified domain. |
| `shutdown`       |                                          | Stops the AdminService.                                                                                        |
| `start`          | `all`, `<domain name>`, `<service name>` | Start a service. `all` starts all enabled services, `<domain name>` starts all enabled services in the specified domain, `<service name>` starts a specific service. Can specify multiple `<domain name>` or `<service name>` arguments.  |
| `start <type>`   | `all`, `<domain name>`                   | Starts a `<type>` of service (`domain`, `nodes`, or `waveforms`). `all` starts all domains, `<domain name>` starts the specified domain.  |
| `status`         | none,`<domain name>`, `<service name>`   | Shows the status of a service. No argument will status all services, `<domain name>` status all services in the specified domain, `<service name>` status a specific service. |
| `status <type>`  | none, `<domain name>`                    | Status a `<type>` of service (`domain`, `nodes`, or `waveforms`). No argument will status in all domains, `<domain name>` status the specified domain.  |
| `stop`           | `all`, `<domain name>`, `<service name>` | Stop a service. `all` stops all running services,  `<domain name>` stops all running services in the specified domain, `<service name>` stops a specific service,  Can specify multiple `<domain name>` or `<service name>` arguments.   |
| `stop <type>`    | `all`, `<domain name>`                   | Stop a `<type>` of service (`domain`, `nodes`, or `waveforms`). `all` stop all domains, '<domain name>' stops the specified domain. |
| `update`         | none, `<domain name>`                    | Reloads the configuration and optionally starts/stops any domain groupings that have changed. Can specify multiple `<domain name>` arguments. |

{{% notice note %}}
**`<type>`** refers to a service type: 'domain', 'nodes', or 'waveforms'.  
**`<domain name>`** value of INI configuration property `DOMAIN_NAME`. For example, domain name is 'REDHAWK_PROD' for the configuration property `DOMAIN_NAME=REDHAWK_PROD`.  
**`<service name>`** derived from the section header `[<type>:<name>]` and `DOMAIN_NAME` property in a service INI file.  For example, service name is `REHAWK_DEV:GppNode` from a node INI file that contains the section `[node:GppNode]` and configuration property `DOMAIN_NAME=REDHAWK_DEV`
{{% /notice %}}

### Running Commands
All commands can be run in either an interactive console mode or from the command line. To run the `list` command using the `rhadmin` client script in interactive mode, enter the following:
```sh
rhadmin -i
```
and when the `rh_admin>` prompt is displayed, enter:
```sh
list
```

To run the same command from the command line, enter the following:
```sh
rhadmin list
```

### Specifying an optional type
Commands that support the optional `<type>` argument (`domain`, `nodes`, or `waveforms`), will execute the specified command against a specific type of service. For example, the following command only starts all Device Managers for the `REDHAWK_DEV` domain;
```sh
rhadmin start nodes REDHAWK_DEV
```
To restart all configured Device Managers in all domains, use the following command:
```sh
rhadmin start nodes all
```
