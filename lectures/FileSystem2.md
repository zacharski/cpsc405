# Files and Directories

## File systems map file names and offsets into physical storage blocks. 


## Directories - naming data
To access a file, the file system needs to translate the file's name to its number. for example


	/home/raz/Code
	
might get translated to `690761`

directories map human readable names to these numbers. We want to group directories hierarchically so that users can group related files.

Implementing this is simple. We will use files to store directories. So if the OS has the file number of a directory it can open up the directory file and scan through the filename/number pairs until it finds the right one.
For example
if we are looking for `foo.txt` we scan the directory and find it has file number 871

![](http://zacharski.org/files/courses/cs405/images/file1.png)

### we still have problems of finding the directory itself. 
This can be recursive

![](http://zacharski.org/files/courses/cs405/images/file2.png)

The file number for the directory /home/raz can be found by looking up raz in the home directory. and the directory home can be found by looking at the root directory.

But recursion needs a base case and that base case is the root directory. The solution is to agree on the file number of the root directory ahead of time. For Unix and Linux systems they use 2 as the file number of the root directory. 

So to find /home/raz/Code/cs405/fib.c

we look at the directory at file number 2. scan the directory for `raz` go to that file number and repeat.

## Directory API
## A QUESTION: since directories are stored as files can we use the same API. The same System Calls to access them (`open/close/read/write`)?

No. we don't want applications to modify directories directly. We want those to be under control of the OS. So we have special commands. `create/mkdir/link/unlink/rmdir` 

## Directory Internals
Many early implementations stored linear lists of file name/ file number in a linked list. (for example Linux `ext2`)

![](http://zacharski.org/files/courses/cs405/images/file3.png)

Works well, but when you have 1,000s of files in a directory -- not so well.

Many recent file systems store directory contents in a tree. (Linux XFS, Microsoft NTFS, Oracle's ZFS)

For example, we can use a B+ tree. 

# Team work. What is a B+ tree and can you work through example

In Linux XFS, B+ tree. It hashes the filename 

Process for finding file number of file name

1. first compute hash of name (0x0000c45e)
2. start at the B+ tree root search for that hash(root at some well known offset in the directory file
3. at each level a tree node contains an array of hash key, file offset pairs that represent a pointer to the child node.

![](http://zacharski.org/files/courses/cs405/images/file4.png)


![](http://zacharski.org/files/courses/cs405/images/file5.png)

## Hard and soft links
Now we can understand hard and soft links.
**hard links** are multiple file directories entries that map different path names to the same file number. Because the file number can appear in multiple directories, file systems must ensure that a file is deleted when the last hard link is removed. To implement this we maintain a reference count. When the reference count is zero we cn remove the file.

**Soft links** map one file name to another name
![](http://zacharski.org/files/courses/cs405/images/file6.png)

## FIles: Finding Data
Once a file system has translated a file name to a file number  the file system must be able to find the blocks associated with that file.

## Microsoft FAT: File Allocation Table file system
dates from the late 1970s.  The file allocation table is an array of 32 bit entries in a reserved area of the volume. Each file in the file system corresponds to a linked list of FAT entries. Each entry contains a pointer to the next entry or an end-of-file marker.

Suppose we have

* one file foo.txt that has a file number of 9
* another bar.txt that has a file number of 12

![](http://zacharski.org/files/courses/cs405/images/file7.png)

## FAT 32 supports 2^28 blocks and files up to 2^32 -1 bytes


## QUESTIONS How much memory to cache entire FAT table

* 10GB drive 1KB blocks (answer 10M entries = 40MB)
* 1TB drive 1KB blocks (answer 1B entries = 8GB)

math

	10GB = 10,000,000,000 / 1000 = 10,000,000 = 10M entries
	2^x = 10M?   = 2^24 so I would say 30MB
	
## MORE QUESTIONS

* sequential file read
* random read
* reliability.  (no transactions)

can make block size 256k but wastes a lot of space

## FFS - The UNIX Fast File System - Fixed Tree

### Efficient for both small and large files

uses an asymmetric tree called a **multilevel index** as illustrated:

![](http://zacharski.org/files/courses/cs405/images/file8.png)

* Each file is a tree with fixed sized blocks (e.g., 4k)
* each file's tree is rooted in an **inode** that contains metadata (file owner, permissions, creation time, modification time, whether directory or not)
* the inode also contains an array of pointers for locating the files data  blocks.Typically the inode contains 15 pointers. 
* The first 12 are direct pointers., that point directly to the first 12 data blocks of a file. 
* The 13th pointer is an indirect pointer. points to a node of the tree called the **indirect block**.  
* The indirect block contains an array of direct pointers. (with 4KB blocks and 4-byte pointers an indirect block can contain 1024 pointers, which allows for files up to 4MB)
* The 14th pointer is a double indirect pointer. Points to the **double indirect block** containing an array of indirect pointers pointing to indirect blocks. so 1024^2 blocks of data
* The 15th pointer is a triple indirect pointer. 1024^3 blocks of data.  (4TB file)

### All the inodes are in an inode array  that is stored in a fixed location on the disk.
a file's number called an *inumber* is an index into the inode array. 

To open a file, 

1. in the directory find the inumber associated with the file number
2. use the inumber to go to the inode
3. find the metadata

## QUESTION - UMW File System
Each inode contains

* 12 direct pointers
* 1 indirect
* 1 double indirect
* 1 triple indirect
* 1 quadruple indirect
* assume 4KB blocks and 8-byte pointers . 
* What is the maximum file size for UMWFS?

Answer

* 12 direct pointers (48K)
* 1 indirect.  512 pointers per block  = 2^9  so 2^9 2^12 blocks = 2^21 = 2MB
* 1 double indirect.   1GB
* 1 triple indirect.  512GB
* 1 quadruple indirect.  256.5TB

### MANAGE free space w/ BIT MAP

## NTFS - New Technology File System. released in 1993
tree with extents

### extends are contiguous regions on the storage device -- contiguous blocks.

similar to the Fast File System. There is an **MFT** master file table. each entry is 1KB and stores a sequence of variable sized attribute records that can store either data or metadata. 

sometimes attribute is small can can be stored directly  (*resident*)

other times too large to fit (*non resident*) stores a pointer.
![](http://zacharski.org/files/courses/cs405/images/file9.png)

this illustrates a large file with multiple extents.  a multi-GB file can be stored in a few extents if the blocks are contiguous

for a small file the data can be stored within the MFT entry
![](http://zacharski.org/files/courses/cs405/images/file10.png)

### MFT has a flexible format


![](http://zacharski.org/files/courses/cs405/images/file11.png)

3 common metadata attributes include

* **standard information** file's creation time, owner ID, security specifier, read only etc.
* **file name**
* **attribute list** the metadata may be larger than what an MFT record can hold.SO it spans multiple records. the attribute list indicates which attributes are in which record.


### need to defrag


# copy on write file systems (COW)

when updating an existing file a COW file system will not overwrite the existing data or meta data, instead, they write a new version to a new location.

With non COW systems ---To make a change to a file -- writing a C source code file ---might  require 3 changes

* Inode - change file size
* pointer block to add a new block
* finally the new block

### motivation for COW

* these small writes are expensive. particularly as new tech. faster drives etc
* small writes are expensive on RAID. 
* small writes are expensie on SSD - any write requires clearing a large erasureblock.

## implementation principles

in a traditional system a file's indirect nodes and data blocks can be anywhere on disk.  given a file's inumber we can find its inode in a fixed location on disk


![](http://zacharski.org/files/courses/cs405/images/file12.png)

In the COW version, we don't want to overwrite inodes in place so we make them mobile.  We store them in a file rather than in a fixed array. But now we need to find them.
![](http://zacharski.org/files/courses/cs405/images/file13.png)

One way is to have the root inode in a fixed location. but even the root inode we want copy on write. 


#### Solution
keep a small array of root inodes each having a monotonically increasing version number and a checksum. If a crash occurs during the write of one, we use the newest inode with a correct checksum.


when we update a block we write it and all of the blocks on the path from it to the root to new locations. 

![](http://zacharski.org/files/courses/cs405/images/file14.png)

# ZFS is a copy on write file system

