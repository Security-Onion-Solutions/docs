# Kernels

The Security Onion ISO image contains the standard RedHat kernel and the Oracle UEK kernel. It should default to the newer Oracle UEK kernel series. If you don't need the older RedHat kernel series, you may opt to remove it:

```
sudo dnf remove kernel kernel-core kernel-modules kernel-modules-core kernel-tools kernel-tools-libs
```
This can be beneficial in a number of ways. First, you will see fewer reboot prompts. Additionally, this will reduce your disk usage in the /boot partition.
