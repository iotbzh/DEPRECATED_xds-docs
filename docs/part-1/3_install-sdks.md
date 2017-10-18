# Installing AGL SDKs

To build your AGL services or AGL applications using XDS you must install a SDK
matching the target/board you want to use. A SDK is a package that includes all
tools you need to cross-build and cross-debug your service/application.

You can generate the AGL SDK for the board you want to use or you can download
a pre-packaged AGL SDK.

## Installing SDK cross-toolchain

`xds-server` uses SDK cross-toolchain installed into directory pointed by
`sdkRootDir` setting (see `xds-server` configuration chapter for more details).

For now, you can only install SDK using a bash script but in a near future you
will be able to manage SDKs using XDS Dashboard.

So to install a SDK, use provided `install-agl-sdks` script:

```bash
# Optional - Log into the container (only necessary when xds-server is running within a docker container)
seb@laptop ~$ ssh -p 2222 devel@localhost

# For example, Install ARM64 SDK (automatic download)
devel@docker ~$ sudo /opt/AGL/xds/server/xds-utils/install-agl-sdks.sh --arch aarch64
```

You may also want to install an SDK that you built manually (see [Part 1 - SDK-compilation-installation](http://docs.automotivelinux.org/docs/devguides/en/dev/reference/sdk-devkit/docs/part-1/1_7-SDK-compilation-installation.html))

```bash
# Optional - Log into the container (only necessary when xds-server is running within a docker container)
seb@laptop ~$ ssh -p 2222 devel@localhost

# Install Intel corei7-64 SDK (using an SDK tarball that has been built or downloaded manually)
devel@docker ~$ sudo /opt/AGL/xds/server/xds-utils/install-agl-sdks.sh --arch corei7-64 --file /tmp/poky-agl-glibc-x86_64-agl-demo-platform-crosssdk-corei7-64-toolchain-
4.0.1.sh
```

<!-- warning -->
>**Warning:** due to some limitation, you need for now to restart `xds-server` in
order to make new installed SDK visible/available.
> ```bash
> seb@laptop ~$  ssh -p 2222 devel@localhost
> devel@docker ~$  systemctl --user restart xds-server.service
> ```
<!-- endwarning -->
