# XDS - X(cross) Development System Agent

XDS-agent is a client that should run on your local / user development machine when you use XDS.

This agent takes care, among others, of starting [Syncthing](https://syncthing.net/)
tool to synchronize your project files from your local host to XDS build server
machine or container (where `xds-server` is running).

> **SEE ALSO**: [xds-server](https://github.com/iotbzh/xds-server), a web server
used to remotely cross build applications.

## Configuration

xds-agent configuration is driven by a JSON config file (named `agent-config.json`).
The tarball mentioned in previous section includes this file with default settings.

Here is the logic to determine which `agent-config.json` file will be used:

1. from command line option: `--config myConfig.json`
1. `$HOME/.xds/agent/agent-config.json` file
1. `/etc/xds-agent/agent-config.json` file
1. `<xds-agent executable dir>/agent-config.json` file

Supported fields in configuration file are (all fields are optional and example
below corresponds to the default values):

- **httpPort** : http port of agent REST interface
- **logsDir**  : directory to store logs (eg. syncthing output)
- **xds-apikey** : xds-agent api-key to use (always set value to "1234abcezam")
- **syncthing.binDir** : syncthing binaries directory (default: executable directory)
- **syncthing.home"** : syncthing home directory (usually .../syncthing-config)
- **syncthing.gui-address** : syncthing gui url (default http://localhost:8384)
- **syncthing.gui-apikey** : syncthing api-key to use (default auto-generated)

```json
{
    "httpPort": "8010",
    "logsDir": "/tmp/logs",
    "xds-apikey": "1234abcezam",
    "syncthing": {
        "binDir": ".",
        "home": "${HOME}/.xds/agent/syncthing-config",
        "gui-address": "http://localhost:8384",
        "gui-apikey": "1234abcezam",
    }
}
```

>**NOTE:** environment variables are supported by using `${MY_VAR}` syntax.

## Start-up

Simply to start `xds-agent` executable

```bash
./xds-agent &
```

>**NOTE** if need be, you can increase log level by setting option
`--log <level>`, supported *level* are: panic, fatal, error, warn, info, debug.

You can now use XDS dashboard and check that connection with `xds-agent` is up.
(see also [xds-server README](https://github.com/iotbzh/xds-server/blob/master/README.md#xds-dashboard))

## Build xds-agent from scratch

### Dependencies

Install and setup [Go](https://golang.org/doc/install) version 1.8 or
higher to compile this tool.

>**NOTE:** for Ubuntu, you can use a PPA, see [https://github.com/golang/go/wiki/Ubuntu](https://github.com/golang/go/wiki/Ubuntu)

### Building

Clone this repo into your `$GOPATH/src/github.com/iotbzh` and use delivered Makefile:

```bash
 mkdir -p $GOPATH/src/github.com/iotbzh
 cd $GOPATH/src/github.com/iotbzh
 git clone https://github.com/iotbzh/xds-agent.git
 cd xds-agent
 make all
```

And to install xds-agent (by default in `/usr/local/bin`):

```bash
make install
```

>**NOTE:** Used `DESTDIR` to specify another install directory
>```bash
>make install DESTDIR=$HOME/opt/xds-agent
>```

#### Cross build

For example on a Linux machine to cross-build for Windows, just follow these steps.

The first time you need to install all the windows-amd64 standard packages on
your system with

```bash
# List all supported OS / ARCH
go tool dist list

# Install all standard packages for another OS/ARCH (eg. windows amd64)
GOOS=windows GOARCH=amd64 go install -v -a std
```

Then compile and package xds-agent using provided makefile

```bash
export GOOS=windows
export GOARCH=amd64
make all
make package
```
