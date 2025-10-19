---
date: 2025-10-19
tags: [Linux, Rocky]
title: Rocky Linux 10 First Installation
summary:
categories: [Linux]
---

# Rocky Linux 10: First Installation

Rocky Linux 10 was released on 11-Jun-2025, which ships with GCC 14.2.1, Python 3.12.9,  and the Linux kernel 6.12.0 etc, see the [Rocky Linux Release and Version Guide](https://wiki.rockylinux.org/rocky/version/), the [news article](https://rockylinux.org/news/rocky-linux-10-0-ga-release) and the [release notes](https://docs.rockylinux.org/release_notes/10_0/).


<!-- more -->

It is found that xrdp appears missing from even the EPEL repository (e.g., see [this thread in the Rocky Linux Forum](https://forums.rockylinux.org/t/cannot-find-install-xrdp-on-rocky-linux-10-dnf-search-no-match/19573)).  This is likely related to the replacements of XOrg Server with Wayland  and VNC with the Remote Desktop Protocol for default graphical remote access.

To enable the graphical remote access, we can use the  GNOME's built-in remote desktop feature, please see [Mahdi's article](https://itstorage.net/index.php/ldce/laa/588-rocky10rdp) for setup.

Often we would like to change the port number for enhancing security, please see [this discourse thread](https://discourse.gnome.org/t/possible-to-use-custom-ports-for-desktop-sharing-remote-login/21884) for the steps.

For further information about the replacement of Xorg server, please see [Red Hat Enterprise Linux 10 plans for Wayland and Xorg server ](https://www.redhat.com/en/blog/rhel-10-plans-wayland-and-xorg-server).


