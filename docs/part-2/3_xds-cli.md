# xds-cli: command-line tool for XDS

`xds-cli` is a command-line tool for X(cross) Development System.

## Configuration

`xds-cli` configuration is defined either by environment variables or by
setting command line options.
Configuration through environment variables may also be defined in a file that
will be sourced by `xds-cli` on start-up. Use `--config|-c` option or set
`XDS_CONFIG` environment variable to specify the config file to use.

So configuration is driven either by environment variables or by command line
options or using a config file knowing that the following priority order is used:

1. use option value (for example use project ID set by `--id` option),
1. else use variable `XDS_xxx` (for example `XDS_PROJECT_ID` variable) when a
   config file is specified with `--config|-c` option,
1. else use `XDS_xxx` (for example `XDS_PROJECT_ID`) environment variable

<!-- note -->
**Note:** all parameters after a double dash (--) are considered as the command
to execute on xds-server.
<!-- endnote -->

### Global Options / Configuration variables

Following is the list of global options across all sub-commands.

__`--config|-c` option or `XDS_CONFIG` env variable__

Env config file to source on startup

__`--log|-l` option or `XDS_LOGLEVEL` env variable__

Logging level, supported levels are:

- panic,
- fatal,
- error,
- warn,
- info,
- debug

Default level is "error".

__`--rpath` option or `XDS_PATH` env variable__

Relative path into project

__`timestamp|-ts` option or `XDS_TIMESTAMP` env variable__

Prefix output with timestamp

__`url` option or `XDS_AGENT_URL` env variable__

Local XDS agent url (default: "localhost:8800")

## Commands

### projects

`projects` (short `prj`) command should be used to managed XDS projects.
This command supports following sub-commands:

```bash
add, a      Add a new project
get         Get a property of a project
list, ls    List existing projects
remove, rm  Remove an existing project
sync        Force synchronization of project sources
```

Here are some usage examples:

```bash
# Create/declare a new project
xds-cli prj add --label "ABeautifulName" --type pm -p /home/seb/xds-workspace/myProject -sp /home/devel/xds-workspace/myProject

# List projects
xds-cli prj ls

# Delete an existing project
xds-cli prj rm 8e49
```

### sdks

`sdks` (alias `sdk`) command should be used to managed cross SDKs.
This command supports following sub-commands:

```bash
add, a      Add a new SDK
get         Get a property of a SDK
list, ls    List installed SDKs
remove, rm  Remove an existing SDK
```

Here are some usage examples:

```bash
# List existing SDKs
xds-cli sdks ls

# Get SDK info
xds-cli sdks get c64d
```

### exec

`exec` command should be used to exec command through XDS system. For example
you can use this command to build your project in XDS system.
This command supports following sub-commands:

`exec` command options are:

__`--id` option or `XDS_PROJECT_ID` env variable (**mandatory option**)__

project ID you want to build

__`--rpath` (short `-p`) or `XDS_RPATH` env variable__

relative path into project

__`--sdkid` (alias `--sdk`) or `XDS_SDK_ID` env variable (**mandatory option**)__

Cross Sdk ID to use to build project

Here are some usage examples:

```bash
cd $MY_PROJECT_DIR
mkdir build

# Generate build system using cmake
xds-cli exec --id=4021 --sdkid=c226 -- "cd build && cmake .."

# Build the project
xds-cli exec --id=4021 --sdkid=c226 -- "cd build && make all"
```

In case of `xds-agent` is not running on default url:port (that is `localhost:8800`)
you can specify the url using `--url` option :

```bash
xds-cli --url=http://localhost:8800 exec --id=4021 --sdkid=c226 -- "cd build && make all"
```

### misc

`misc` command allows to execute miscellaneous sub-commands such as:

```bash
version, v   Get version of XDS agent and XDS server
status, sts  Get XDS configuration status (including XDS server connection)
```

Here are some usage examples:

```bash
xds-cli misc version --verbose

xds-cli misc sts
```

## How to build

### Prerequisites

 You must install and setup [Go](https://golang.org/doc/install) version 1.8.1 or
 higher to compile this tool.

### Building

Clone this repo into your `$GOPATH/src/github.com/iotbzh` and use delivered Makefile:

```bash
 export GOPATH=$(realpath ~/workspace_go)
 mkdir -p $GOPATH/src/github.com/iotbzh
 cd $GOPATH/src/github.com/iotbzh
 git clone https://github.com/iotbzh/xds-cli.git
 cd xds-cli
 make
```

## Debug

Visual Studio Code launcher settings can be found into `.vscode/launch.json`.

>**Tricks:** To debug both `xds-cli` and `xds-agent` (REST API part) or common
code `xds-common`, it may be useful use the same local sources.
So you should replace `xds-agent` + `xds-common` in `vendor` directory by a symlink.
So clone first `xds-agent` + `xds-common` sources next to `xds-cli` directory.
You should have the following tree:

```bash
> tree -L 3 src
src
|-- github.com
    |-- iotbzh
       |-- xds-agent
       |-- xds-cli
       |-- xds-common
```

Then invoke `vendor/debug` Makefile rule to create a symlink inside vendor
directory :

```bash
cd src/github.com/iotbzh/xds-cli
make vendor/debug
```
