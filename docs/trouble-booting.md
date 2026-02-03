# Trouble Booting

If you have trouble booting the ISO image, here are some troubleshooting steps:

-  If you're trying to create a bootable USB from an ISO image, try using Balena Etcher which can be downloaded at <https://www.balena.io/etcher/>.
-  Certain display adapters may require the `nomodeset` option passed to the kernel (see <https://unix.stackexchange.com/questions/353896/linux-install-goes-to-blank-screen>).
-  If you're still having problems with our 64-bit ISO image, try downloading the standard x86-64 ISO image for Oracle Linux 9. If it doesn't run, then you should double-check your 64-bit compatibility.

!!! TIP
    
    If all else fails but standard x86-64 Oracle Linux 9 installs normally, then you can install our components on top of it as described in the [Network Installation](network-installation.md) section. However, please keep in mind that network installations are not supported.