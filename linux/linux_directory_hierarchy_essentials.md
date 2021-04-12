linux_directory_hierarchy_essentials.md
# Linux Directory Hierarchy Essentials

The details of the Linux directory structure are outlined in the Filesystem Hierarchy Standard, or FHS.

## Important subdirectories:
* `/bin` Contains **excutables**, including most of the basic Unix commands such as `ls` and `cp`.
* `/dev` Contains **device** files.
* `/etc` This core system **configuration** directory contains the user password, boot, device, netwoking, and other setup files. Many items in `/etc` are specific to the machine's **hardware**.
* `/home` Holds personal directories for regular users. 
* `/lib` An abbreviation for **library**, this directory holds library files containing code that executables can use. There are two types of libraries: **static** and **shared**. The `/lib` directory should contain only **shared** libraries. (-> `/usr/lib`)
* `/proc` Provides system statistics through a browsable directory-and-file interface. The `/proc` directory contains informaion about currently **running processes** as well as some kernel parameters.
* `/sys` This directory is similar to `/proc` in that it provides a device and system interface.
* `/sbin` The place for **system executables**. Programs in `/sbin` directories relate to **system management**, so regular users usually do not have `/sbin` components in their command paths. Many of the utilities found here will not work if you're not running them as root.
* `/tmp` A storage area for smaller, **temporary files** that you don't care much about. **Any** user may read to and write from `/tmp`, but the user may not have permission to access another user's files there. Many programs use this directory as a **workspace**. Most distributions clear `/tmp` when the machine boots and some even remove its old files periodically.
* `/usr` Contains a large directory hierarchy, including the bulk of the **Linux system**. Many of the directory names in `/usr` are the same as those in the **root** directory, and they hold the same type of files. (The reason that root directory does not contain the complete system is promarily historic -- in the past, it was to keep space requirements low for the root.)
* `/var` The **variable** subdirectory, where programs record runtime information. **System logging**, user tracking, caches, and other files that system programs create and manage are here. 

## Other Root Subdirectories:
* `/boot` Contains **kernel boot loader** files. These files pertain only to the very first stage of the Linux startup procedure.
* `/media` A base attachment point for removable media such as flash drives that is found in many distributions.

## The `/usr` Directory
`/usr` is where most of the user-space programs and data reside. In addition to `/usr/bin`, `/usr/sbin` and `/usr/lib`, `/usr` contains the following:
* `/include` Holds **header files** used by the **C compiler**.
* `/info` Contains **GNU info manuals**.
* `/man` COntains manual pages.

## Kernel Location
On Linux systems, the kernel is normally in `/vmlinuz` or `/boot/vmlinuz`. A **boot loader** loads this file into memory and sets it in motion when the system boots.

Once the boot loader runs and sets the kernel in motion, the main kernel file is no longer used by the running system. 





## References
* [About FHS](https://www.pathname.com/fhs/)
* How Linux Works
