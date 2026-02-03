# First Time Users

Welcome, first time users! You're going to be peeling back the layers of your network in just a few minutes! 

First, please note that Security Onion only supports x86-64 architecture (standard Intel or AMD 64-bit processors). If you don't have an x86-64 box available, then one option may be to run Security Onion in the cloud. For more information, please see the [Amazon Cloud](cloud-amazon.md), [Azure Cloud](cloud-azure.md), and [Google Cloud](cloud-google.md) sections. 

Otherwise, if you have an x86-64 box for your Security Onion IMPORT installation, then check to make sure it meets the MINIMUM hardware requirements of 4GB RAM, 2 CPU cores, and 200GB of storage. If you will be installing Security Onion in a virtual machine, then the VM will need those specs at minimum and the host machine will have higher hardware requirements since it will be running the host operating system and possibly other VMs or apps. For more information about virtualization, please see the [VMware](vmware.md), [VirtualBox](virtualbox.md), and [Proxmox](proxmox.md) sections. Once you've verified that you have an appropriate installation target, you can proceed to download our ISO image as shown in the [Download](download.md) section and then install the ISO image as shown in the [Installation](installation.md) section.

Once you have Security Onion installed either in the cloud or on-prem, you can configure for IMPORT as shown below (also see the [Configuration](configuration.md) section). 

Once you're comfortable with your IMPORT installation, then you can move on to more advanced installations as shown in the [Architecture](architecture.md) section.

After booting the ISO image, the boot menu appears:

![Image](images/01_grub.png)

When prompted, enter your desired username and password:

![Image](images/02_initial_install.png)

Once installation is complete, you are prompted to reboot:

![Image](images/03_initial_install_finished.png)

After rebooting, login using the username and password that you specified and then Setup will start automatically:

![Image](images/04_setup_init.png)

Perform a standard installation:

![Image](images/05_setup_option.png)

When prompted for installation type, select IMPORT:

![Image](images/06_setup_type.png)

If your Security Onion machine has full Internet access as described in the [Firewall](firewall.md) section, select Standard. Otherwise, select [Airgap](airgap.md):

![Image](images/06_setup_airgap.png)

Review the license and agree:

![Image](images/07_setup_license.png)

Set the hostname:

![Image](images/08_setup_hostname.png)

If you use the default hostname of `securityonion`, you will see a warning:

![Image](images/09_setup_hostname_conflict.png)

Select your management interface:

![Image](images/10_setup_mn_nic.png)

Select static IP addressing (recommended) or DHCP:

![Image](images/11_setup_mn_int.png)

Specify IP address and CIDR mask:

![Image](images/12_setup_cidr.png)

Set gateway address:

![Image](images/13_setup_gateway.png)

Enter DNS servers:

![Image](images/14_setup_dns_servers.png)

Configure DNS search domain:

![Image](images/15_setup_dns_domain.png)

If necessary, you can change the default Docker IP range:

![Image](images/16_setup_docker_range.png)

If you are connected to the Internet, select whether it is direct or via proxy:

![Image](images/18_setup_direct_proxy.png)

Create username for [Security Onion Console](security-onion-console.md):

![Image](images/20_setup_webuser.png)

Set password for [Security Onion Console](security-onion-console.md):

![Image](images/21_setup_webpass1.png)

Confirm password for [Security Onion Console](security-onion-console.md):

![Image](images/22_setup_webpass2.png)

Select how to access [Security Onion Console](security-onion-console.md):

![Image](images/23_setup_access_type.png)

Allow connections through the host-based firewall if necessary:

![Image](images/26_setup_so_allow.png)

Specify an IP address or range to allow through the host-based firewall:

![Image](images/27_setup_so_allow_input.png)

Confirm all options:

![Image](images/28_setup_summary.png)

Setup complete:

![Image](images/29_setup_finished.png)

Login to [Security Onion Console](security-onion-console.md):

![Image](images/37_login.png)

After logging in, you will see the [Security Onion Console](security-onion-console.md) Overview page:

![Image](images/38_overview.png)

Go to the [Grid](grid.md) page, click the button to expand the node, and then verify all services are running properly:

![Image](images/39_grid.png)

While on the [Grid](grid.md) page, you can import a PCAP or EVTX file using the upload button at the bottom of the screen:

![Image](images/40_upload.png)

Once the import is complete, the [Grid](grid.md) page should display a message at the of the page and provide a link to [Dashboards](dashboards.md) to view all alerts and logs from the import:

![Image](images/45_import.png)

If you want to see just the alerts, you can go to the [Alerts](alerts.md) page although you may need to manually adjust the time range:

![Image](images/50_alerts.png)

If you find something interesting on the [Alerts](alerts.md) or [Dashboards](dashboards.md) pages, you may want to use the Correlate or Hunt actions to find related logs on the [Hunt](hunt.md) page:

![Image](images/56_hunt.png)

If you find interesting network traffic, you can pivot to full packet capture via the [PCAP](pcap.md) action:

![Image](images/62_pcap.png)

You can change the view to ASCII transcript for a more human readable view of the traffic:

![Image](images/65_pcap_details.png)

If you need to refer back to previous PCAP jobs, you can find them on the [PCAP](pcap.md) page:

![Image](images/72_jobs.png)

IMPORT installations do not support remote agents, but if you were running a production installation you could download the Elastic Agent installer from [Downloads](downloads.md):

![Image](images/78_downloads.png)

The [Administration](administration.md) section allows you to manage user accounts:

![Image](images/81_users.png)

It also allows you to manage Grid members:

![Image](images/84_gridmembers.png)

The [Administration](administration.md) section also allows you to configure various aspects of the system:

![Image](images/87_config.png)

It also allows you to upload a license key for additional enterprise features:

![Image](images/91_licensekey.png)

If you made it to the end of this First Time Users section, congratulations! If you have any questions or problems, please see the [Help](help.md) section. If you like Security Onion, please consider sharing on social media about Security Onion to help spread the word. Thanks!