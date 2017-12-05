# Create your first AGL application

## Prerequisites

- `xds-agent` is running locally on your machine
  (see previous chapter: [Installing XDS client tools](./1_install-client.md) )
- `xds-server` is running locally in a docker container or is accessible on your
  network (see previous chapter: [Installing XDS server](./2_install-xds-server.md))
- one or more SDK have been installed (see previous chapter: [Installing AGL SDKs](3_install-sdks.md))
- XDS configuration is correct: in other words, connection is correctly
  established between `xds-agent` and `xds-server` and no error message is
  displayed when you open XDS Dashboard in a web browser.

## Setup

Let's use _helloworld-native-application_ project as example, so you need first to clone
this project into a directory that will be accessible by `xds-server`.
Depending of the project sharing method:

- Cloud sync: you can clone project anywhere on your local disk,
- Path mapping: you must clone project into `$HOME/xds-workspace` directory.

<!-- note -->
**Note:** : [helloworld-native-application](https://github.com/iotbzh/helloworld-native-application) project is an AGL
project based on [app-templates](https://git.automotivelinux.org/apps/app-templates/)
(included as a git submodule). This CMake templating, used to develop application
with the AGL Application Framework, will automatically generate makefile rules
(eg. `remote-target-populate`) or scripts (eg. `build/target/xxx` scripts).

For more info about app-template, please refer to [this documentation](http://docs.automotivelinux.org/docs/devguides/en/dev/reference/sdk-devkit/docs/part-2/2_4-Use-app-templates.html).
<!-- endnote -->

### Clone project

```bash
cd $HOME/xds-workspace
git clone --recursive https://github.com/iotbzh/helloworld-native-application.git
```

### Declare project using XDS Dashboard

Use XDS Dashboard to declare your project. Open a browser and connect to XDS
Dashboard. URL depends of your config, for example `http://localhost:8800`

Open the right sidebar and select `Projects` entry to open project page and then
create/declare a new project by with the plus icon:

![](./pictures/xds-dashboard-icon-2.png){:: style="margin:auto;"}

<!-- pagebreak -->

Set `Sharing Type` and paths according to your needs.

![](./pictures/xds-dashboard-prj-1.png){:: style="width:90%;"}

Note that XDS creates (if not already exists) a file named `xds-project.conf`
when you declare a new project. This file may be very useful when you plan to
use XDS client tools such as `xds-cli` or `xds-gdb`.

<!-- note -->
**Note:** when `Path mapping` type is selected, you must clone your project into
`$HOME/xds-workspace` directory (named **Local Path** in modal window).

If XDS server is running in the XDS docker container (see [Installation based on Docker container](./2_install-xds-server.md#installation-based-on-docker-container) ),
the **Server Path** must be set to `/home/devel/xds-workspace/xxx` where xxx is your
project directory name.

If you select `Cloud Sync`, you can clone your project wherever you want on
your local disk.
<!-- endnote -->

### Declare project from command line

Use XDS command line tool named [xds-cli](./part2/3_xds-cli.md) to declare your
project from command line and more precisely the `projects add` command
(short option: `prj add`).

```bash
xds-cli prj add --label="Project_helloworld-native-application" --type=pm --path=/home/seb/xds-workspace/helloworld-native-application --server-path=/home/devel/xds-workspace/helloworld-native-application
```

> **Note:** option `--url=http://localhost:1234` may be added to `xds-cli` in
> order to set url of `xds-agent` in case of agent is not running on default
> port (for example here, 1234)

<!-- pagebreak -->

## Build from XDS dashboard

Open the build page build entry of left sidebar ![](./pictures/xds-dashboard-icon-3.png){:: style="display:inline; padding:0;"},

then select your **Project** and the **Cross SDK** you want to use and click on
**Clean / Pre-Build / Build / Populate** buttons to execute various build actions.

![](./pictures/xds-dashboard-prj-2.png){:: style="width:90%;"}

## Build from command line

You need to determine which is the unique id of your project. You can find
this ID in project page of XDS dashboard or you can get it from command line
using `xds-cli` tool and `projects list` command (short: `prj ls`):

```bash
xds-cli prj ls
ID                                       Label                                   LocalPath
f9904f70-d441-11e7-8c59-3c970e49ad9b     Project_helloworld-service              /home/seb/xds-workspace/helloworld-service
4021617e-ced0-11e7-acd2-3c970e49ad9b     Project_helloworld-native-application   /home/seb/xds-workspace/helloworld-native-application
```

XDS tools, including `xds-cli` are installed by default in `/opt/AGL/bin`
directory and this path has been added into your PATH variable. If it is not the
case, just add it manually using `export PATH=${PATH}:/opt/AGL/bin` command line.

Now to refer your project, just use --id option or use `XDS_PROJECT_ID`
environment variable.

<!-- note -->
**Note:** short id notation is also supported as soon as given id value is non ambiguous.
For example, to refer to Project_helloworld-native-application project listed
in above command, you can simply use --id 40 instead of --id 4021617e-ced0-11e7-acd2-3c970e49ad9b
<!-- endnote -->

You also need to determine the ID of the cross SDK you want to use to cross build
you application. To list installed SDK, use the following command:

```bash
xds-cli sdks ls
List of installed SDKs:
  ID                                    NAME
  7aa19224-b769-3463-98b1-4c029d721766  aarch64  (3.99.1+snapshot)
  41a1efc4-8443-3fb0-afe5-8313e0c96efd  corei7-64  (3.99.2+snapshot)
  c226821b-b5c0-386d-94fe-19f807946d03  aarch64  (3.99.3)
```

You are now ready to use XDS to for example cross build your project.
Here is an example to build a project based on CMakefile:

```bash
# Go into your project directory and create a build directory
cd $MY_PROJECT_DIR
mkdir build

# Generate build system using cmake
xds-cli exec --id=4021617e --sdkid=c226821b -- "cd build && cmake .."

# Build the project
xds-cli exec --id=4021617e --sdkid=c226821b -- "cd build && make all"
```

<!-- note -->
**Note:** If you use `&&`, `||` or `;` statement in the executed command line,
you need to double quote the command, for example `"cd build && make`.
<!-- endnote -->

To avoid to set project id, sdks id, url, ... for each command line, you can
define these settings as environment variables within an env file and just set
`--config` option or source file before executing xds-cli command.

Note that XDS creates a file named `xds-project.conf` (only if not already exists)
when you declare a new project using XDS Dashboard (or using `xds-cli prj add...`).
Edit this file if needed and then refer it with `--config` option.

For example, the equivalence of above command is:

```bash
# MY_PROJECT_DIR=/home/seb/xds-workspace/helloworld-native-application
cd $MY_PROJECT_DIR

# Edit and potentially adapt xds-project.conf file that has been created
# automatically on project declaration using XDS Dashboard
cat xds-project.conf
  # XDS project settings
  export XDS_AGENT_URL=localhost:8800
  export XDS_PROJECT_ID=4021617e-ced0-11e7-acd2-3c970e49ad9b
  export XDS_SDK_ID=c226821b-b5c0-386d-94fe-19f807946d03

# Create build directory and invoke cmake and then build project
xds-cli exec --config=./xds-project.conf -- "mkdir -p build && cd build && cmake .."
cd build && xds-cli exec --config=../xds-project.conf -- "make all"

# Or equivalent by first sourcing conf file (avoid to set --config option)
source xds-project.conf
xds-cli exec "mkdir -p build && cd build && cmake .."
cd build && xds-cli exec "make all"
```

<!-- note -->
**Note:** all parameters after a double dash (--) are considered as the command
to execute.
<!-- endnote -->

## Build from IDE

First create an XDS config file or reuse the previous one, for example we use
here aarch64 SDK to cross build application for a Renesas Gen3 board.

```bash
# create file at root directory of your project
# for example:
# MY_PROJECT_DIR=/home/seb/xds-workspace/helloworld-native-application
cat > $MY_PROJECT_DIR/xds-project.conf << EOF
 export XDS_AGENT_URL=localhost:8800
 export XDS_PROJECT_ID=4021617e-ced0-11e7-acd2-3c970e49ad9b
 export XDS_SDK_ID=c226821b-b5c0-386d-94fe-19f807946d03
EOF
```

### NetBeans

This chapter will show you how to create 2 configurations, one to compile your
project natively (using native GNU gcc) and one to cross-compile your project
using XDS. You can easily switch from one to other configuration using menu
**Run -> Set Project Configuration**.

__Netbeans 8.x :__

- Open menu **Tools** -> **Options**
  - Open **C/C++** tab, in **Build Tools** sub-tab, click on **Add** button:

    ![Add new tool panel](./pictures/nb_newtool.png)

  - Then, you should set **Make Command** and **Debugger Command** to point to xds tools:

    ![Add new tool panel](./pictures/nb_xds_options.png)

  - Finally click on **OK** button.

- Now create we first declare project into NetBeans and create first a native
  configuration. To do that, open menu **File** -> **New Project**

- Select **C/C++ Project with Existing Sources** ;
  Click on **Next** button

- Specify your project directory and set **Select Configuration Mode** to
  **Custom**. Keep **Tool Collection** to **Default GNU** in order to create a
  *native configuration* based on native GNU GCC. Finally click on **Next** button.

    ![Select Model panel](./pictures/nb_new-project-1.png)

- Just update **Run in Folder** field and add `build_native` suffix so that
  resulting build files will be located into `build_native` sub-directory.
  Keep all others settings to default value and  click on **Next** button.

    ![Select Model panel](./pictures/nb_new-project-2.png)

- Click several times on **Next button** (always keep default settings) and
  click on **Finish** button to complete creation of native configuration.

- Now we will create a **cross configuration** based on XDS tools.
  Edit project properties (using menu **File** -> **Project Properties**) to add a new configuration that will use XDS to cross-compile your application for example for a Renesas Gen3 board.

  - in **Build** category, click on **Manage Configurations** button and then **New** button to add a new configuration named for example "Gen3 board"

    ![Select Build category](./pictures/nb_new-project-3.png)

  - Click on **Set Active** button

  - Select **Pre-Build** sub-category, and set:
    - Working Directory: `build_gen3`
    - Command Line: `xds-cli exec -c ../xds-project.conf -- cmake -DRSYNC_TARGET=root@renesas-gen3 -DRSYNC_PREFIX=/opt ..`
    - Pre-build First: `ticked`

  - Select **Make** sub-category, and set:
    - Working Directory: `build_gen3`
    - Build Command: `xds-cli exec -c ../xds-project.conf -- make remote-target-populate`
    - Clean Command: `xds-cli exec -c ../xds-project.conf -- make clean`

    ![Select Make sub-category](./pictures/nb_new-project-4.png)

  - Select **Run** sub-category, and set:
    - Run Command: `target/start-on-root@renesas-gen3.sh`
    - Run Directory: `build-gen3`

    ![Select Run  sub-category](./pictures/nb_new-project-5.png)

  - Click on **OK** button to save settings

By changing configuration from **Default** to **Gen3 board**, you can now simply
compile your helloworld application natively (**Default** configuration) or
cross-compile your application through XDS for the Renesas Gen3 board
(**Gen3 board** configuration).

### Visual Studio Code

Open your project in VS Code

```bash
cd $MY_PROJECT_DIR
code . &
```

Add new tasks : press `Ctrl+Shift+P` and select the `Tasks: Configure Task`
command and you will see a list of task runner templates.

And define your own tasks, here is an example to build
[helloworld-native-application](https://github.com/iotbzh/helloworld-native-application)
AGL helloworld application based on cmake template.

```json
{
    "version": "2.0.0",
    "type": "shell",
    "presentation": {
        "reveal": "always"
    },
    "tasks": [
        {
            "taskName": "clean",
            "command": "/bin/rm -rf ${workspaceFolder}/build/* && mkdir -p build && echo Cleanup done.",
            "problemMatcher": []
        },
        {
            "taskName": "pre-build",
            "group": "build",
            "command": "/opt/AGL/bin/xds-cli exec --rpath build --config xds-project.conf -- cmake -DRSYNC_TARGET=root@renesas-gen3 -DRSYNC_PREFIX=/opt ../",
            "problemMatcher": [
                "$gcc"
            ]
        },
        {
            "taskName": "build",
            "group": "build",
            "command": "/opt/AGL/bin/xds-cli exec --rpath build --config xds-project.conf -- make widget",
            "problemMatcher": [
                "$gcc"
            ]
        },
        {
            "taskName": "populate",
            "command": "/opt/AGL/bin/xds-cli exec --rpath build --config xds-project.conf -- make widget-target-install",
            "problemMatcher": []
        }
    ]
}
```

> **Note:** You can also add your own keybindings to trig above tasks, for example:
>
> ```json
> // Build
> {
>   "key": "alt+f9",
>   "command": "workbench.action.tasks.runTask",
>   "args": "clean"
> },
> {
>   "key": "alt+f10",
>   "command": "workbench.action.tasks.runTask",
>   "args": "pre-build"
> },
> {
>   "key": "alt+f11",
>   "command": "workbench.action.tasks.runTask",
>   "args": "build"
> },
> {
>   "key": "alt+f12",
>   "command": "workbench.action.tasks.runTask",
>   "args": "populate"
> },
> ```
>
> More details about VSC keybindings [here](https://code.visualstudio.com/docs/editor/tasks#_binding-keyboard-shortcuts-to-tasks)
>
> More details about VSC tasks [here](https://code.visualstudio.com/docs/editor/tasks)

#### Qt Creator

Please refer to [agl-hello-qml](https://github.com/radiosound-com/agl-hello-qml#clone--build-project) project.
Thanks to Dennis for providing this useful example.

#### Others IDE

*Coming soon...*
