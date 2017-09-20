# xds-gdb: wrapper on gdb for XDS

`xds-gdb` is a wrapper on gdb debugger for X(cross) Development System.

This tool allows you to debug an application built with an xds-server without
the need to install gdb or any cross tool.

Two debugging models are supported:

1. native debugging

1. XDS remote debugging requiring an XDS server and allowing cross debug your
  application.

 By default XDS remote debug is used and you need to define `XDS_NATIVE_GDB`
variable to use native gdb debug mode instead.

> **SEE ALSO**: [xds-server](https://github.com/iotbzh/xds-server), a web server
used to remotely cross build applications.
> **SEE ALSO**: [xds-exec](https://github.com/iotbzh/xds-exec),
wrappers on `exec` command that allows to cross build your application through `xds-server`.

## Configuration

 `xds-gdb` configuration is defined by variables (see listed below).
 These variables may be set using :

- environment variables (inherited),
- or a config file set with `XDS_CONFIG` environment variable, for example:
  `XDS_CONFIG=/tmp/my_xds_gdb_config.env xds-gdb`
- or by setting variables within a gdb ini file (see details below),
- or a "user" config file located in following directory (first found is taken):
  1. $(CURRENT_DIRECTORY)/.xds-gdb.env
  1. $(CURRENT_DIRECTORY)/../xds-gdb.env
  1. $(CURRENT_DIRECTORY)/target/xds-gdb.env
  1. $(HOME)/.config/xds/xds-gdb.env

### Configuration Variables

 `XDS_CONFIG` :
 Config file defining `XDS_xxx` configuration variables. Variables of this file
 will overwrite inherited environment variables. Variables definition may be 
 prefixed or not by "export" keyword.
 Here is an example of config file

```bash
cat $HOME/myProject/xds-gdb.env

export XDS_SERVER_URL=http://xds-docker:8000
export XDS_PROJECT_ID=IW7B4EE-DBY4Z74_myProject
export XDS_SDK_ID=poky-agl_aarch64_4.0.1
```

`XDS_LOGLEVEL`

Set logging level (supported levels: panic, fatal, error, warn, info, debug)

`XDS_LOGFILE`

Set logging file, default `/tmp/xds-gdb.log`.

`XDS_NATIVE_GDB`

Use native gdb mode instead of remote XDS server mode.

`XDS_PROJECT_ID`  *(mandatory with XDS server mode)*

Project ID you want to build

`XDS_RPATH`

Relative path into project

`XDS_SDK_ID`   *(mandatory with XDS server mode)*

Cross Sdk ID to use to build project

`XDS_SERVER_URL`    *(mandatory with XDS server mode)*

Remote XDS server url

### Configuration variables set within gdb init command file

Above `XDS_xxx` variables may also be defined within gdb init command file 
(see --command or -x option of genuine Gdb).  
You must respect the following syntax: commented line including `:XDS-ENV:` tag

Example of gdb init file where we define project and sdk ID:

```bash
     # :XDS-ENV: XDS_PROJECT_ID=IW7B4EE-DBY4Z74_myProject
     # :XDS-ENV: XDS_SDK_ID=poky-agl_aarch64_4.0.1
```

## How to build xds-gdb from scratch

### Prerequisites

 You must install and setup [Go](https://golang.org/doc/install) version 1.7 or
 higher to compile this tool.

### Building

Clone this repo into your `$GOPATH/src/github.com/iotbzh` and use delivered Makefile:

```bash
 export GOPATH=$(realpath ~/workspace_go)
 mkdir -p $GOPATH/src/github.com/iotbzh
 cd $GOPATH/src/github.com/iotbzh
 git clone https://github.com/iotbzh/xds-gdb.git
 cd xds-gdb
 make
```

## Debug

Visual Studio Code launcher settings can be found into `.vscode/launch.json`.
