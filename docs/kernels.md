# Kernels

Security Onion standardizes on the Oracle Unbreakable Enterprise Kernel (UEK). Nodes run the UEK8 (6.x) kernel series, and the standard EL9 RedHat compatible kernel (RHCK, 5.14) is removed automatically once a node is actually running UEK8.

This has a number of benefits. First, you will see fewer reboot prompts. Additionally, this will reduce your disk usage in the `/boot` partition.

## Upgrading to UEK8

If a node is still running the standard EL9 kernel or the older UEK7 (5.x) kernel, you can move it to UEK8 by running the following command on that node:

```
sudo so-kernel-upgrade
```

This installs the UEK8 kernel and makes it the boot default. It does not reboot the node, so you can schedule the reboot yourself. The new kernel does not take effect until you reboot.

!!! NOTE

    The manager mirrors the UEK8 packages and serves them to the rest of the grid. If the manager has not synced them yet, `so-kernel-upgrade` on the manager will sync them for you; on any other node it will tell you to sync the manager first.

## Removal of the EL9 Kernel

Once a node reboots and comes up on UEK8, the next highstate removes the standard EL9 kernel packages:

```
kernel kernel-core kernel-modules kernel-modules-core kernel-tools kernel-tools-libs
```

The removal is deliberately deferred until after the reboot. `dnf` refuses to erase the running kernel, and waiting also means the node has proven it boots on UEK8 before its fallback is deleted. Fresh installs reboot at the end of setup and are cleaned up on the first highstate after that; existing nodes are cleaned up whenever you reboot them.

If you would rather not wait for the next highstate, you can run the cleanup directly:

```
sudo so-kernel-upgrade --cleanup
```

This does nothing unless the node is already running UEK8.

!!! NOTE

    Older UEK7 `kernel-uek` 5.x packages are not removed by this cleanup. They age out on their own as newer kernels are installed.
