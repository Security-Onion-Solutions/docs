# SO-CRATES

From <https://so-crates.org/>:

> SO-CRATES (Security Onion Containerized Rapid Analysis of Threats, Evil, and Sus) is a standalone web application for analyzing pcap files, log files, email messages, and binary files. Features include Suricata network analysis (with tshark adding the protocols Suricata doesn't parse), YARA binary scanning, Sigma rule detection for logs, email header/link/attachment analysis, and a single-page UI for browsing alerts, metadata, transcripts, and hexdumps.

## Usage

SO-CRATES is a part of our [Security Onion Desktop](security-onion-desktop.md) installation. To start it, open SO-CRATES from the desktop menu, next to [NetworkMiner](networkminer.md) and [Wireshark](wireshark.md).

SO-CRATES runs in a container, which is only downloaded the first time you open it. That first time, a terminal window lists what will be downloaded and waits for you to press Enter (or Ctrl-C to cancel). It then installs Podman and downloads the SO-CRATES container image from `ghcr.io`, so this requires Internet access and `sudo` privileges. After that, SO-CRATES starts without prompting, even when offline.

When SO-CRATES is running, it opens in your web browser at `http://127.0.0.1:8000`. It only listens on localhost, and keeps its data in `~/socrates-data`. To stop SO-CRATES, press Ctrl-C in its terminal window.

!!! NOTE

    If your Security Onion Desktop is on an air-gapped network, see <https://so-crates.org/installation/podman/#air-gapped-offline-deployment-for-podman>.

## Versions

Each Security Onion Desktop package pins a specific SO-CRATES release, and Security Onion updates that package as new SO-CRATES releases come out. To run a different version, pass its tag to `so-crates` in a terminal. For example:

```
so-crates 5.3.0
```

## More Information

!!! NOTE

    For more information about SO-CRATES, please see <https://so-crates.org/>.
