---
title: "Troubleshooting"
weight: 2000
version: "5.1"
product: "Sensu Go"
platformContent: false
menu:
  sensu-go-5.1:
    parent: guides
---

## Service logging

Logs produced by Sensu services -- i.e. sensu-backend and sensu-agent -- are
often the best place to start when troubleshooting a variety of issues.

### Log levels

Each log message is associated with a log level, indicative of the relative severity of the event being
logged:

| Log Level          | Description |
|--------------------|------------------------------------------------------------|
| panic              | 😰 An error caused the service to exit uncleanly           |
| fatal              | 👋 An error caused the service to exit cleanly (status 0)  |
| error              | ❗❗ You should definitely look into this                  |
| warn               | ❗❓ Maybe you should look into this?                       |
| info               | 👌 Just so you know                                         |
| debug              | 📈 Verbose output describing lower-level service operations |

These log levels can be configured by specifying the desired log level as the
value of `log-level` in the service configuration file (e.g. `agent.yml` or
`backend.yml` configuration files), or as an argument to the `--log-level`
command line flag:

{{< highlight shell >}}
sensu-agent start --log-level debug
{{< /highlight >}}

Changes to log level via configuration file or command line arguments require
restarting the service. For guidance on restarting a service, please
consult the Operating section of the [agent][agent-ref] or
[backend][backend-ref] reference, respectively.

### Log file locations

Sensu services print [structured log messages][structured] to standard output.
In order to capture these log messages to disk or another logging facility, Sensu services
make use of capabilities provided by the underlying operating service's service
management. For example, logs are sent to the journald when systemd is the service manager,
whereas log messages are redirected to `/var/log/sensu` when running under sysv
init schemes.

In the table below, the common targets for logging and example commands for
following those logs are described. The name of the desired service, e.g.
`backend` or `agent` may be substituted for `${service}` variable.

| Platform     | Version           | Target | Command to follow log |
|--------------|-------------------|--------------|-----------------------------------------------|
| RHEL/Centos  | >= 7       | journald     | {{< highlight shell >}}journalctl --unit sensu-${service} --follow{{< /highlight >}}   |
| RHEL/Centos  | <= 6       | log file     | {{< highlight shell >}}tail --follow /var/log/sensu/sensu-${service}{{< /highlight >}} |
| Ubuntu       | >= 15.04   | journald     | {{< highlight shell >}}journalctl --unit sensu-${service} --follow{{< /highlight >}}   |
| Ubuntu       | <= 14.10   | log file     | {{< highlight shell >}}tail --follow /var/log/sensu/sensu-${service}{{< /highlight >}} |
| Debian       | >= 8       | journald     | {{< highlight shell >}}journalctl --unit sensu-${service} --follow{{< /highlight >}}   |
| Debian       | <= 7       | log file     | {{< highlight shell >}}tail --follow /var/log/sensu/sensu-${service}{{< /highlight >}} |
| Windows      | Any        | log file     | {{< highlight powershell >}}Get-Content -  Path "C:\scripts\test.txt" -Wait{{< /highlight >}}

_NOTE: Platform versions described above are for reference only and do not
supercede the documented [supported platforms][platforms]._

[structured]: https://dzone.com/articles/what-is-structured-logging
[journalctl]: https://www.digitalocean.com/community/tutorials/how-to-use-journalctl-to-view-and-manipulate-systemd-logs
[platforms]: ../../getting-started/platforms
[agent-ref]: ../../reference/agent/#restarting-the-service
[backend-ref]: ../../reference/backend/#restarting-the-service
