# PXP Agent

This is the agent for the PCP Execution Protocol [(PXP)][1], based on the
the Puppet Communications Protocol [(PCP)][2]. It enables the execution of
[actions][3] on remote nodes, via PXP. The pxp-agent needs to be connected to a
[PCP broker][8] in order to be used; please refer to the documentation below
for how to do that.

## Building from source

### Build requirements
 - a C++11 compiler (clang/gcc 4.7)
 - gnumake
 - cmake
 - boost
 - ruby (2.0 and newer)

Build with make and make install

## Configuring
 To do that, you
need to specify the secure WebSocket URL of

## Modules

[Actions][3] are grouped in modules, by which they can be loaded and configured
within pxp-agent. An example of module is given by the [Puppet module][4].

### Modules interface

A module is a file that provides the module metadata and an interface to
execute its actions.

The module metadata is a JSON object containing the following entries:
 - `configuration`: an object that specifies options that may be included in a separate configuration file (see below);
 - `actions`: a JSON array containing specifications for the `input` and `output` of each action.

For a trivial example of module, please refer to the [reverse module][5] used
for testing.

pxp-agent will execute a module to retrieve its metadata (by passing no
argument) and to run a given action (by invoking the action with the input
specified in the [PXP request][6]). Normally when pxp-agent calls a module it
must be executable. When the `interpreter` field is specified in the
configuration file (see below), pxp-agent will use this value to execute the
module instead.

Note that the [transaction status module][7] is implemented natively; there is
no external file for it.

### Modules configuration

Modules can be configured by placing a configuration file in the
`--modules-config-dir`. Config options must be specified in the module metadata
(see above). Module config files are named like `module_name.conf`.

Module configuration files may specify the `interpreter` field.  For
example:

```
{
    "interpreter" : "/opt/puppetlabs/puppet/bin/ruby"
}
```

## Configuring the agent

The PXP agent is configured with a config file. The values in the config file
can be overridden by supplying arguments on the command line.

The agent will look for the default config file in:
 - \*nix: */etc/puppetlabs/pxp-agent/pxp-agent.conf*
 - Windows: *C:\ProgramData\PuppetLabs\pxp-agent\etc\pxp-agent.conf*

A different config file can be specified by passing the `--config-file` option.

The config files use the JSON format. Options must be specified as entries of a
single JSON object. Example:

```
{
    "broker-ws-uri" : "wss://127.0.0.1:8090/pcp/",
    "key" : "/etc/puppetlabs/puppet/ssl/private_keys/myhost.net.pem",
    "ca" : "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
    "cert" : "/etc/puppetlabs/puppet/ssl/certs/myhost.net.pem"
}
```

Note that you have to specify the WebSocket secure URL of the [PCP broker][8]
in order to establish the WebSocket connection on top of which the PCP
communication will take place.

### Starting unconfigured

If no broker WebSocket URI, SSL key, ca or cert value is supplied, the agent
will still be able to start in unconfigured mode. In this mode no connection
will be established but the process will not terminate.

### Daemon

The agent will execute as a daemon unless the `--foreground` flag is specified.
During its execution, the daemon PID will be stored in:
 - \*nix: */var/run/puppetlabs/pxp-agent.pid*
 - Windows: *C:\ProgramData\PuppetLabs\pxp-agent\var\run\pxp-agent.pid*

### Logging

## *nix

By default, log messages will be written to
*/var/log/puppetlabs/pxp-agent/pxp-agent.log*.

You can specify a different file with the `--logfile` option.

When running in foreground mode, it is possible to display log messages on
console by using an hyphen instead of a file path: `--logfile -`.

## Windows

By default, log messages are written to the stdout stream.

You can specify a log file with `--logfile` option.

## Log level

The default log level is `info`. You can specify a different log level by
using the `--loglevel` option with one of the following strings: `none`,
`trace`, `debug`, `info`, `warning`, `error`, `fatal`.

### List of all configuration options

The PXP agent has the following configuration options

**config-file (optional)**

Specify which config file to use.

**broker-ws-uri (required to connect)**

The WebSocket URI of the PXP broker you wish to connect the agent to, example
wws://192.168.0.1:8061/pxp/

**ssl-ca-cert (required to connect)**

The location of your SSL Certificate Authority certificate, example
/etc/puppet/ssl/ca/ca_crt.pem

**ssl-cert (required to connect)**

The location of the pxp-agent SSL certificate, example /etc/puppet/ssl/certs/bob_crt.pem

**ssl-key (required to connect)**

The location of the pxp-agent's SSL private key, example /etc/puppet/ssl/certs/bob_key.pem

**logfile (optional)**

The path of the log file.

**loglevel (optional)**

Specify one of the following logging levels: *none*, *trace*, *debug*, *info*,
*warning*, *error*, or *fatal*; the default one is *info*

**modules-dir (optional)**

Specify the directory where modules are stored

**modules-config-dir (optional)**

Specify the directory containing the configuration files of modules

**spool-dir (optional)**

The location where the outcome of non-blocking requests will be stored; the
default location is:
 - \*nix: */opt/puppetlabs/pxp-agent/spool*
 - Windows: *C:\ProgramData\PuppetLabs\pxp-agent\var\spool*

**foreground (optional flag)**

Don't become a daemon and execute on foreground on the associated terminal.

**pidfile (optional; only on *nix platforms)**

The path of the PID file; the default is */var/run/puppetlabs/pxp-agent.pid*

## Starting the agent

The agent can be started as a daemon by running
```
pxp-agent
```
in the ./build/bin directory

[1]: https://github.com/puppetlabs/pcp-specifications/blob/master/pxp/README.md
[2]: https://github.com/puppetlabs/pcp-specifications/blob/master/pcp/README.md
[3]: https://github.com/puppetlabs/pcp-specifications/blob/master/pxp/actions.md
[4]: https://github.com/puppetlabs/pxp-agent/blob/master/modules/pxp-module-puppet
[5]: https://github.com/puppetlabs/pxp-agent/blob/master/lib/tests/resources/modules/reverse_valid
[6]: https://github.com/puppetlabs/pcp-specifications/blob/master/pxp/request_response.md
[7]: https://github.com/puppetlabs/pcp-specifications/blob/master/pxp/transaction_status.md
[8]: https://github.com/puppetlabs/pcp-broker
