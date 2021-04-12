volume_analysis_memo.md
# Volume Analysis

Learning Objectives
* Understand how disk works and how data is structured on the surface of disk drive platters
* Know about common types of disk partitioning systems
* Understand fundamental concepts of the most commonly encountered partition system and DOS-style partition system


the most common source of digital evidence is ***hard disk*** or ***hard disk drive*** (HDD)

## Hard Disk Geometory and Disk Partitioning
#### Definitions and common concepts
* Table: a collection of related data arranged in a predefined structure
* Table entry: all of them assembled in an orderly manner. (0, 1, 2, ... A unique number is assigned to each entry)
* Cluster: A group of data blocks (typically 4kB) and the ***smallest allocation*** of storage space assigned by an operation system
* Cluster chain: A groupt of clusters which make up an ****entire*** file.
* Data block: The ***smallest unit*** of storage of a device (typically 512 bytes). Also known as a ***sector***
* Dataset: A collection of data blocks (e.g. disk image) ***from which files are to be recovered***
* File header: The first data block of a file which contains a ***beginning-of-file marker***
* File fragment: A group of sequential data blocks that make up ***only*** part of the ***complete*** file and separate from the rest file by unrelated data blocks
* File footer: The last data block of a file which contains an end-of-file marker.
```
FILE
| header | fragment | footer |
```

#### Hard Disk Geometry
A disk consists of platters. Each platter has two magnetic surfaces, where data is stored.  
Disks use magnetic storage technology, and each platter surface requires its own dedicated head to read/write data

