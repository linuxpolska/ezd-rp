



# What is LP-EZD?


Our package contains set of necessary services which allow  to lunch EZD-RP application developed by NASK on Kubernetes using [Helm](https://github.com/helm/helm).

<h1 align="center" style="border-bottom: none">
    <a href="https://linuxpolska.com/pl/" target="_blank"><img alt="Longhorn" width="200px" src="https://github.com/linuxpolska/ezd-rp/blob/release/1.0.0/docs/LinuxPolska-icon.png""></a>
</h1>



## Table of content
- [Before you begin](PREREQUISITES.md)
- [Installation](INSTALLATION.md)
- [GUI Installation](INSTALLATION_GUI.md)
- [Troubleshooting](TROUBLESHOOTING.md)


## Getting Started

The best way to get started is with the  ["Before you begin"](PREREQUISITES.md), ["Installation"](INSTALLATION.md) and ["GUI Installation"](INSTALLATION_GUI.md)
section in the documentation.


## Package versioning

Versioning for charts:


Example versioning `1.2.3`:

* `1.` - adding new functionality for instance: new chelm chart with minio, postfix server, change mongodb chart on totally different
* `2.` - update version of helm chart for instance from mongodb 4.x to 7.x, modify content of helm charts
* `3.` - change default values in helm chart, for instance tag version, default parameters in existing release of charts


Image versioning

Example versioning `0.15.0-el-9-r1`:

* `0.15.0` - version of rpm package use in solution or any number if you build main service from sources during container build
* `el-9` - version of image uses in section FROM (containerfile). For instance `FROM  quay.io/eurolinux/eurolinux-9:latest`
* `r1` - changes in dockerfile 


## Releases
| Release   | Chart Version   | First Stable Version | Status         | Release Notes                                                  |  Tested with NASK Ezdrp application     | Active Maintenance |
|-----------|-----------------|----------------------|----------------|----------------------------------------------------------------|-----------------------------------------|-------------------|
| **1.0.0***| 1.0.0           | 1.0.0                | Stable         | [🔗] (https://github.com/linuxpolska/ezd-rp/releases/tag/1.0.0)| Chart up to 1.15.84 and Application version up to 1.2023-15 |                   |
| **1.1.1***| 1.1.1           | 1.0.0                | Stable         | [🔗] (https://github.com/linuxpolska/ezd-rp/releases/tag/1.1.1)| Chart up to 1.15.84 and Application version up to 1.2023-15 |                   |
| **1.2.1***| 1.2.1           | 1.0.0                | Stable         | [🔗] (https://github.com/linuxpolska/ezd-rp/releases/tag/1.2.1)| Chart up to 1.15.84 and Application version up to 1.2023-15 |                   |
| **1.3.1***| 1.3.1           | 1.0.0                | Stable         | [🔗] (https://github.com/linuxpolska/ezd-rp/releases/tag/1.3.1)| Chart up to 19.4.15 and Application version up to 1.2024-19.4 |                  |
| **1.4.1***| 1.4.1           | 1.0.0                | Stable         | [🔗] (https://github.com/linuxpolska/ezd-rp/releases/tag/1.4.1)| Chart up to 19.7.15 and Application version up to 1.2024-19.7 |               ✅         |
## Contact

Please use the following to reach members of the community:


- GitHub:  If you have any questions start a [discussion](https://github.com/linuxpolska/ezd-rp/discussions) or if you want make changes open an [issue](https://github.com/linuxpolska/ezd-rp//issues)  
- Web Page: If you need commercial support [contact with us](https://linuxpolska.com/pl/kontakt/)

We are supporting polish and english language.
