# Installing XDS server

Depending of your configuration, this step is necessary or not.

IOW **you are a developer and plan to use/connect to an existing `xds-server`**
running on your local network (On-Premise config) or in the Cloud (SaaS config),
**you don't need to install the server part and you can skip this step**.

For others (standalone config or administrators that want to install an
On-Premise solution) xds-server must be installed.

Several installation types are supported :

| Install type | Supported OS | Section to refer |
|--------------|--------------|------------------|
| Container | Linux or MacOS | [see Installation based on Docker container](#docker_container) |
| Virtual Machine | Linux, MacOS or Windows | [see Installation based on VirtualBox appliance](#vbox_appliance) |
| Native | Linux | [see Native installation](#native) |

## <a name="docker_container"></a> Installation based on Docker container

### Prerequisites

Docker is installed on the host machine, please refer to [Docker documentation](https://docs.docker.com/engine/installation/) for more details.

### Get the container

Load the pre-build AGL SDK docker image including `xds-server`:

```bash
seb@laptop ~$ wget -O - http://iot.bzh/download/public/2017/XDS/docker/docker_agl_worker-xds-latest.tar.xz | docker load
```

You should get `docker.automotivelinux.org/agl/worker-xds:X.Y` image

```bash
# List image that we just load
seb@laptop ~$ docker images "docker.automotivelinux.org/agl/worker-xds*"

docker.automotivelinux.org/agl/worker-xds       4.0              786d65b2792c        6 days ago          654MB
```

### Create and start a new container

Use provided script to create a new docker container and start it:

```bash
# Get script
seb@laptop ~$ wget https://raw.githubusercontent.com/iotbzh/xds-server/master/scripts/xds-docker-create-container.sh

# Create new XDS worker container
seb@laptop ~$ bash ./xds-docker-create-container.sh

# Check that new container is running
seb@laptop ~$ docker ps | grep worker-xds

b985d81af40c        docker.automotivelinux.org/agl/worker-xds:3.99.1       "/usr/bin/wait_for..."   6 days ago           Up 4 hours          0.0.0.0:8000->8000/tcp, 0.0.0.0:69->69/udp, 0.0.0.0:10809->10809/tcp, 0.0.0.0:2222->22/tcp    agl-xds-seb@laptop-0-seb
```

### Check if xds-server is running

`xds-server` is automatically started as a service on container startup.

To check if xds-server is correctly install and running, you can access the web
interface, what we call the "Dashboard", using a web browser :

```bash
# if container is running on your local host
# (else replace localhost by the name or the ip of the machine running the container)
seb@laptop ~$ xdg-open http://localhost:8000
```

### Container settings

This container (ID=0) exposes following ports:

- 8000 : `xds-server` to serve XDS Dashboard
- 69   : TFTP
- 2222 : ssh

This container also creates the following volumes (sharing directories between
inside and outside docker):

| Directory on host | Directory inside docker | Comment |
|-------------------|-------------------------|---------|
| $HOME/xds-workspace | /home/devel/xds-workspace | XDS projects workspace location|
| $HOME/ssd/xdt_0 | /xdt | location to store SDKs |
| $HOME/devel/docker/share |/home/devel/share | another shared directory |

<!-- note -->
Please refer to **part 2 - xds-server** documentation for additional info.
<!-- endnote -->

## <a name="vbox_appliance"></a> Installation based on VirtualBox appliance

_coming soon ..._

## <a name="native"></a> Native installation

<!-- note -->
Only Linux host OS is supported and tested for native installation !
<!-- endnote -->

### Install packages for debian distro type

```bash
# 'DISTRO' can be set to { xUbuntu_16.04, xUbuntu_16.10, xUbuntu_17.04, Debian_8.0, Debian_9.0}
seb@laptop ~$  export DISTRO="xUbuntu_16.04"

seb@laptop ~$  wget -O - http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/Release.key | sudo apt-key add -
seb@laptop ~$  sudo bash -c "cat >> /etc/apt/sources.list.d/AGL.list <<EOF
deb http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/ ./
EOF"

seb@laptop ~$  sudo apt-get update
seb@laptop ~$  sudo apt-get install agl-xds-server
```

### Install packages for openSUSE distro type

```bash
# DISTRO can be set to {openSUSE_Leap_42.2, openSUSE_Leap_42.3, openSUSE_Tumbleweed}
seb@laptop ~$  export DISTRO="openSUSE_Leap_42.2"

seb@laptop ~$  sudo zypper ar http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/isv:LinuxAutomotive:app-Development.repo

seb@laptop ~$  sudo zypper ref
seb@laptop ~$  sudo zypper install agl-xds-server
```

### Configure xds-server

<!-- note -->
>**Optional step**: nothing to do if you keep default settings
<!-- endnote -->

When `xds-server` is started as a systemd service, default environment variables
are set into `/etc/default/xds-server` file.

`xds-server` configuration is also driven by a JSON config file (`config.json`),
and default JSON config is `/etc/xds-server/config.json`.

<!-- note -->
>**Note**: you can use your own JSON config by settings `APP_CONFIG` variable of
`/etc/default/xds-server` file to your file, for example
`/home/MYUSER/.xds/server/config.json`
<!-- endnote -->

Supported fields in JSON configuration file are :

- **httpPort** : HTTP port of client webapp / dashboard
- **webAppDir** : location of client dashboard (default: webapp/dist)
- **shareRootDir** : root directory where projects will be copied
- **logsDir**  : directory to store logs (eg. syncthing output)
- **sdkRootDir** : root directory where cross SDKs are installed
- **syncthing.binDir** : syncthing binaries directory (default: executable directory)
- **syncthing.home"** : syncthing home directory (usually .../syncthing-config)
- **syncthing.gui-address** : syncthing gui url (default http://localhost:8384)
- **syncthing.gui-apikey** : syncthing api-key to use (default auto-generated)

All fields are optional and example below corresponds to the default values:

```json
{
    "httpPort": 8000,
    "webAppDir": "webapp/dist",
    "shareRootDir": "${HOME}/.xds-server/projects",
    "logsDir": "/tmp/logs",
    "sdkRootDir": "/xdt/sdk",
    "syncthing": {
        "binDir": "./bin",
        "home": "${HOME}/.xds-server/syncthing-config",
        "gui-address": "http://localhost:8384",
        "gui-apikey": "123456789",
    }
}
```

>**NOTE:** environment variables are supported by using `${MY_VAR}` syntax.

### Start/Stop xds-server

`xds-server` can be managed as a systemd service with the following commands:

```bash
# Status XDS server:
seb@laptop ~$ sudo systemctl status xds-server.service

# Stop XDS server
seb@laptop ~$ sudo systemctl stop xds-server.service

# Start XDS server
seb@laptop ~$ sudo systemctl start xds-server.service

# Get XDS server logs
seb@laptop ~$ sudo journalctl --unit=xds-server.service --output=cat
```

To check if xds-server is correctly install and running, you can access the web
interface, what we call the "Dashboard", using a web browser :

```bash
seb@laptop ~$ xdg-open http://localhost:8000
```
