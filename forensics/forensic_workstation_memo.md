# Building a Forensic Workstation

## The Sleuth Kit (TSK)

4 main forensic layers
1. File System Layer
2. Content/Data Layer
3. Metadata/Inode Layer
4. File Layer

Prefix '***fs***': used to display general ***file system details***. Popular file systems such as ***File Allocation Table*** (FAT), ***fourth extended filesystem*** (ext4), ***New Technologies File System*** (NTFS).

Prefix '***blk***': search and recovery of ***actual information***. E.g. recovery of ***deleted content***

Prefix '***i***': collecting ***metadata*** like inodes/entries, timestamp, addresses and size data.

Prefix '***f***': gathering data based on the name of files.

## Virtualization

The virtualization technology has been introduced as a solution that ***several*** operating systems and applications can be run on ***one*** physical computer, known as "host".


virtualization is an abstraction layer in the computer architecture model where an operating system communicates with this layer, instead of directly communicating with the hardware.