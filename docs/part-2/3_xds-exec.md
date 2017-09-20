# xds-exec: wrapper on exec for XDS

`xds-exec` is a wrapper on exec linux command for X(cross) Development System.

As well as `xds-exec` is a wrapper on exec command and can be use to execute any
command on a remote `xds-server`.

This tool can be used in lieu of "standard" `exec` command to execute any
command on a remote `xds-server`. For example you can trig your project build by
executing : `xds-exec --config conf.env -- make build`

## Configuration

`xds-exec` configuration is defined either by environment variables or by
setting command line options (see listed below).

Configuration through environment variables may also be defined in a file that
will be sourced on `xds-exec` start-up. Use `--config|-c` option or set
`XDS_CONFIG` environment variable to specify the config filename.

<!-- note -->
>**Note:** all parameters after a double dash (--) are considered as the command
to execute on xds-server.
<!-- endnote -->

### Configuration Options/Variables

`--id` option or `XDS_PROJECT_ID` env variable  **(mandatory)**

Project ID you want to build

`--config|-c` option or `XDS_CONFIG` env variable

Env config file to source on startup

`--log|-l` option or `XDS_LOGLEVEL` env variable

Logging level, supported levels are:

- panic,
- fatal,
- error,
- warn,
- info,
- debug

Default level is "error".

`--rpath` option or `XDS_PATH` env variable

Relative path into project

`sdkid` option or `XDS_SDK_ID` env variable  **(mandatory)**

Cross Sdk ID to use to build project

`timestamp|-ts` option or `XDS_TIMESTAMP` env variable

Prefix output with timestamp

`url` option or `XDS_SERVER_URL` env variable

Remote XDS server url (default: "localhost:8000")

## How to build

### Prerequisites

 You must install and setup [Go](https://golang.org/doc/install) version 1.7 or
 higher to compile this tool.

### Building

Clone this repo into your `$GOPATH/src/github.com/iotbzh` and use delivered Makefile:

```bash
 export GOPATH=$(realpath ~/workspace_go)
 mkdir -p $GOPATH/src/github.com/iotbzh
 cd $GOPATH/src/github.com/iotbzh
 git clone https://github.com/iotbzh/xds-exec.git
 cd xds-exec
 make
```

## Debug

Visual Studio Code launcher settings can be found into `.vscode/launch.json`.

>**Tricks:** To debug both `xds-exec` (client part) and `xds-server` (server part),
it may be useful use the same local sources.
So you should replace `xds-server` in `vendor` directory by a symlink.
So clone first `xds-server` sources next to `xds-exec` directory.
You should have the following tree:

```bash
> tree -L 3 src
src
|-- github.com
    |-- iotbzh
       |-- xds-exec
       |-- xds-server
```

Then invoke `vendor/debug` Makefile rule to create a symlink inside vendor
directory :

```bash
cd src/github.com/iotbzh/xds-exec
make vendor/debug
```
