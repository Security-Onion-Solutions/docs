# Kernels

Security Onion standardizes on the Oracle Unbreakable Enterprise Kernel (UEK). Nodes run the UEK8 (6.x) kernel series, and the standard EL9 RedHat compatible kernel (RHCK, 5.14) is removed once a node is actually running UEK8.

This is all automatic and you should not need to do anything. Nodes pick up the UEK8 kernel as part of their normal OS updates, and the stock EL9 kernel is removed for you on the next highstate after the node reboots onto UEK8.

The removal is deliberately deferred until after the reboot. `dnf` refuses to erase the running kernel, and waiting also means the node has proven it boots on UEK8 before its fallback is deleted. Fresh installs reboot at the end of setup and are cleaned up on the first highstate after that; existing nodes are cleaned up whenever you reboot them.

These are the packages removed:

```
kernel kernel-core kernel-modules kernel-modules-core kernel-tools kernel-tools-libs
```

!!! NOTE

    Older UEK7 `kernel-uek` 5.x packages are not removed. They age out on their own as newer kernels are installed.

## Doing It Manually

If a node did not end up on UEK8 on its own, you can run the following command on that node:

```
sudo so-kernel-upgrade
```

This installs the UEK8 kernel and makes it the boot default. It does not reboot the node, so you can schedule the reboot yourself. The new kernel does not take effect until you reboot.

!!! NOTE

    The manager mirrors the UEK8 packages and serves them to the rest of the grid. If the manager has not synced them yet, `so-kernel-upgrade` on the manager will sync them for you; on any other node it will tell you to sync the manager first.

Similarly, if the stock EL9 kernel is still installed on a node that is already running UEK8, you can run the cleanup directly instead of waiting for the next highstate:

```
sudo so-kernel-upgrade --cleanup
```

This does nothing unless the node is already running UEK8.
