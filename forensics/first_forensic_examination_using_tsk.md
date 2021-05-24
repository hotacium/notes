first_forensic_examination_using_tsk
# First Forensic Examination Using TSK

## 0. Download forensic disk image from NIST and Extract

```sh
wget http://www.cfreds.nist.gov/dfr-images/dfr-11-mft-ntfs.dd.bz2
bzip2 -d dfr-11-mft-ntfs.dd.bz2
```

## 1. Use the `mmls` command to discover the layout of the disk image

we can find the ***image offset***, or where the allocated partition starts

```sh
$ mmls -t dos dfr-11-mft-ntfs.dd

DOS Partition Table
Offset Sector: 0
Units are in 512-byte sectors

      Slot      Start        End          Length       Description
000:  Meta      0000000000   0000000000   0000000001   Primary Table (#0)
001:  -------   0000000000   0000000127   0000000128   Unallocated
002:  000:000   0000000128   0002091135   0002091008   NTFS / exFAT (0x07)
003:  -------   0002091136   0002097152   0000006017   Unallocated

```
(`-t`, vstype: volume system type  
-> dos, mac, bsd, sun, gpt)

there is one partition: ***128-2091135*** (length: 2091008)  
(from Sector 128 to Sector 2091135)


## 2. Use the `dcfldd` command
`dcfldd` to extract the partition image from the disk image

```sh
# dcfldd: enhanced version of dd for forensics and security
# if: input file, insted of stdin
# bs: byte size
# of: output file, instead of stdout
$ dcfldd if=dfr-11-mft-ntfs.dd bs=512 skip=128 count=2091008 of=ntfs.dd
```
## 3. Use the `fsstat` command
`fsstat` to display the details of the filesystem made on the partition

```sh
$ fsstat -f ntfs ntfs.dd
# fsstat: display the details of the filesystem

FILE SYSTEM INFORMATION
--------------------------------------------
File System Type: NTFS
Volume Serial Number: 2ACADB0FCADAD5E3
OEM Name: NTFS    
Volume Name: ntfs
Version: Windows XP

METADATA INFORMATION
--------------------------------------------
First Cluster of MFT: 43562
First Cluster of MFT Mirror: 65343
Size of MFT Entries: 1024 bytes
Size of Index Records: 4096 bytes
Range: 0 - 64
Root Directory: 5

CONTENT INFORMATION
--------------------------------------------
Sector Size: 512
Cluster Size: 8192
Total Cluster Range: 0 - 130686
Total Sector Range: 0 - 2091006

$AttrDef Attribute Values:
$STANDARD_INFORMATION (16)   Size: 48-72   Flags: Resident
$ATTRIBUTE_LIST (32)   Size: No Limit   Flags: Non-resident
$FILE_NAME (48)   Size: 68-578   Flags: Resident,Index
$OBJECT_ID (64)   Size: 0-256   Flags: Resident
$SECURITY_DESCRIPTOR (80)   Size: No Limit   Flags: Non-resident
$VOLUME_NAME (96)   Size: 2-256   Flags: Resident
$VOLUME_INFORMATION (112)   Size: 12-12   Flags: Resident
$DATA (128)   Size: No Limit   Flags: 
$INDEX_ROOT (144)   Size: No Limit   Flags: Resident
$INDEX_ALLOCATION (160)   Size: No Limit   Flags: Non-resident
$BITMAP (176)   Size: No Limit   Flags: Non-resident
$REPARSE_POINT (192)   Size: 0-16384   Flags: Non-resident
$EA_INFORMATION (208)   Size: 8-8   Flags: Resident
$EA (224)   Size: 0-65536   Flags: 
$LOGGED_UTILITY_STREAM (256)   Size: 0-65536   Flags: Non-resident
```

## 4. Use the `fls` command
`fls` to peruse the filesystem
(peruse: formal to read something, especially in careful way)

```sh
$ fls -d -r ntfs.dd
# -d: Display deleted entries only
# -r: Recurse on directory entries

d/- * 0:	Orion
-/d * 36-144-1:	Lyra
-/r * 41-128-1:	Lyra/Sheliak.txt
-/r * 42-128-1:	Lyra/Vega.txt
-/r * 43-128-1:	Lyra/Sulafat.txt

```
\* we are not likely to know the names of contents of files deleted, though  


## 5. Recover deleted files using the `icat` command
```sh
# -r: recover deleted file
# 41, 42, 43: MFT entry numbers
$ icat -r ntfs.dd 41 > recovered_Sheliak.txt
$ icat -r ntfs.dd 42 > recovered_Vega.txt
$ icat -r ntfs.dd 43 > recovered_Sulafat.txt
```


