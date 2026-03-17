# SSH

Security Onion uses the latest SSH packages. It does not manage the SSH configuration in `/etc/ssh/sshd_config` with [Salt](salt.md). This allows you to add any PAM modules or enable two factor authentication (2FA) of your choosing.