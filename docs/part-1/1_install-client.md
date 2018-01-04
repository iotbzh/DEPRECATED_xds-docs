# Installing XDS client tools

[xds-agent](https://github.com/iotbzh/xds-agent) is a client tool that must run
on your machine (user / developer host) to be able to use XDS.
You should establish the following chain:

Client (eg. `xds-cli` or XDS Dashboard) and `xds-agent` is running on your machine
and `xds-server` is running on a remote server and/or in a container.
Exchanges between these 3 tools are done though HTTP and Websocket protocols
and default url/port mentioned in schema below can be change using config files.

![XDS blocks chain](./pictures/xds-block-chain.png)

Installation of other XDS client tools, such as `xds-cli` or `xds-gdb` is
optional and depends of what you want to do :

- [xds-cli](https://github.com/iotbzh/xds-cli) : command line tool to used to interact with XDS (also used by IDE integration).
- [xds-gdb](https://github.com/iotbzh/xds-gdb) : requested for debugging application.

> [xds-exec](https://github.com/iotbzh/xds-exec) is another tool used to interact
> with XDS before that `xds-cli` exists. `xds-exec` is deprecated and you should
> now use `xds-cli exec` command instead.

## Install packages for debian distro type

```bash
# 'DISTRO' can be set to { xUbuntu_16.04, xUbuntu_16.10, xUbuntu_17.04, Debian_8.0, Debian_9.0 }
seb@laptop ~$  export DISTRO="xUbuntu_16.04"

seb@laptop ~$  wget -O - http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/Release.key | sudo apt-key add -
seb@laptop ~$  sudo bash -c "cat >> /etc/apt/sources.list.d/AGL.list <<EOF
deb http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/ ./
EOF"

seb@laptop ~$  sudo apt-get update
seb@laptop ~$  sudo apt-get install agl-xds-agent
seb@laptop ~$  sudo apt-get install agl-xds-cli
seb@laptop ~$  sudo apt-get install agl-xds-gdb
```

## Install packages for openSUSE distro type

```bash
# DISTRO can be set to {openSUSE_Leap_42.2, openSUSE_Leap_42.3, openSUSE_Tumbleweed}
seb@laptop ~$  export DISTRO="openSUSE_Leap_42.2"

seb@laptop ~$  sudo zypper ar http://download.opensuse.org/repositories/isv:/LinuxAutomotive:/app-Development/${DISTRO}/isv:LinuxAutomotive:app-Development.repo

seb@laptop ~$  sudo zypper ref
seb@laptop ~$  sudo zypper install agl-xds-agent
seb@laptop ~$  sudo zypper install agl-xds-cli
seb@laptop ~$  sudo zypper install agl-xds-gdb
```

## Install for other platforms (Windows / MacOS)

- Install `xds-agent`:

  1. Download the latest released tarball from github [releases page](https://github.com/iotbzh/xds-agent/releases).

  1. Then unzip the tarball any where into your local disk (for example: `/opt/AGL/xds` or `C:\AGL\xds`).

  1. Add binary to PATH:

      - MacOs: create the .bash_profile `nano .bash_profile` and add `export PATH="/opt/AGL/xds/xds-agent:$PATH`
      - Windows: change the system path via control panel or system settings or
    `setx path "C:\AGK\xds\xds-agent;%path%"`

- repeat the previous steps to install other tools depending of your needs:
  - `xds-cli` : requested for command line and IDE integration. ([released tarball link](https://github.com/iotbzh/xds-cli/releases)).
  - `xds-gdb` : requested for debugging application. ([released tarball link](https://github.com/iotbzh/xds-gdb/releases)).

## Start xds-agent

XDS-agent is a client tool that must run on your local / user development
machine when you use XDS.

For Linux distro, a user systemd service is provided, so you just need to enable
if you want to start it automatically at boot and you can also start it
immediately using following commands:

```bash
# Enable xds-agent service at boot
seb@laptop ~$  systemctl --user enable xds-agent.service

# Manually start xds-agent service
seb@laptop ~$  systemctl --user start xds-agent.service

# Get status and log of xds-agent service
seb@laptop ~$  systemctl --user status xds-agent.service
```
