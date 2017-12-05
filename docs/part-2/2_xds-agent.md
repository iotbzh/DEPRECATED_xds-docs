# XDS - X(cross) Development System Agent

XDS-agent is a client that should run on your local / user development machine when you use XDS.

This agent takes care, among others, of starting [Syncthing](https://syncthing.net/)
tool to synchronize your project files from your local host to XDS build server
machine or container (where `xds-server` is running).

> **SEE ALSO**: [xds-server](https://github.com/iotbzh/xds-server), a web server
used to remotely cross build applications.

## Configuration

xds-agent configuration is driven by a JSON config file.
The tarball mentioned in previous section includes this file with default settings.

Here is the logic to determine which conf file will be used:

1. from command line option: `--config myConfig.json`
1. `$HOME/.xds/agent/agent-config.json` file
1. `/etc/xds-agent/config.json` file

Supported fields in configuration file are (all fields are optional and example
below corresponds to the default values):

- **httpPort** : http port of agent REST interface
- **webAppDir** : location of client webapp / dashboard (default: webapp/dist)
- **logsDir**  : directory to store logs (eg. syncthing output)
- **xdsServers** : an array of xds-server object
  - **xdsServers.url**: url of xds-server to connect to
- **syncthing**: a object defining syncthing settings
  - **syncthing.binDir** : syncthing binaries directory (default: executable directory)
  - **syncthing.home"** : syncthing home directory (usually .../syncthing-config)
  - **syncthing.gui-address** : syncthing gui url (default <http://localhost:8386>)
  - **syncthing.gui-apikey** : syncthing api-key to use (default auto-generated)

```json
{
    "httpPort": "8800",
    "webAppDir": "./www",
    "logsDir": "${HOME}/.xds/agent/logs",
    "xdsServers": [
        {
          "url": "http://localhost:8000"
        }
    ],
    "syncthing": {
        "home": "${HOME}/.xds/agent/syncthing-config",
        "gui-address": "http://localhost:8386",
        "gui-apikey": "1234abcezam"
    }
}
```

>**Note:** environment variables are supported by using `${MY_VAR}` syntax.

## Start-up

Simply to start `xds-agent` executable

```bash
./xds-agent &
```

>**Note:** if need be, you can increase log level by setting option
`--log <level>`, supported *level* are: panic, fatal, error, warn, info, debug.

You can now use XDS dashboard and check that connection with `xds-agent` is up.
(see also [xds-server README](https://github.com/iotbzh/xds-server/blob/master/README.md#xds-dashboard))

## Build xds-agent from scratch

### Dependencies

Install and setup [Go](https://golang.org/doc/install) version 1.8.1 or higher to compile this tool.

>**Note:** for Ubuntu, you can use a PPA, see [https://github.com/golang/go/wiki/Ubuntu](https://github.com/golang/go/wiki/Ubuntu)

Install [npm](https://www.npmjs.com/), [nodejs](https://nodejs.org/en/) and
some other tools

Ubuntu:

```bash
 sudo apt-get install golang npm curl git zip unzip
 sudo npm install --global @angular/cli   # Angular Command Line Interface
 # Install LTS version of nodejs
 sudo n lts
```

openSUSE:

```bash
 sudo zypper install go npm git curl zip unzip
 sudo npm install --global @angular/cli   # Angular Command Line Interface
 # Install LTS version of nodejs
 sudo n lts
```

Don't forget to open new user session after installing the packages.

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

>**Note:** Used `DESTDIR` to specify another install directory
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

## Debugging

### XDS agent architecture

The agent part is written in *Go* and the webapp / dashboard is in *typescript + Angular4*.

```bash
|
+-- bin/                where xds-server binary file will be built
|
+-- conf.d              Linux configuration and startup files (systemd user service)
|
+-- glide.yaml          Go package dependency file
|
+-- lib/                sources of server part (Go)
|
+-- main.go             main entry point of of Web server (Go)
|
+-- Makefile            makefile including
|
+-- README.md           this readme
|
+-- scripts/            hold various scripts used for installation or startup
|
+-- tools/              temporary directory to hold development tools (like glide)
|
+-- vendor/             temporary directory to hold Go dependencies packages
|
+-- webapp/             source client basic webapp / dashboard
```

### Debug

Visual Studio Code launcher settings can be found into `.vscode/launch.json`.

>**Tricks:** To debug both `xds-agent` and `xds-server` or common code
`xds-common`, it may be useful use the same local sources.
So you should replace `xds-server` + `xds-common` in `vendor` directory by a symlink.
So clone first `xds-server` + `xds-common` sources next to `xds-agent` directory.
You should have the following tree:

```bash
> tree -L 3 src
src
|-- github.com
    |-- iotbzh
       |-- xds-agent
       |-- xds-common
       |-- xds-server
```

Then invoke `vendor/debug` Makefile rule to create a symlink inside vendor
directory :

```bash
cd src/github.com/iotbzh/xds-agent
make vendor/debug
```
