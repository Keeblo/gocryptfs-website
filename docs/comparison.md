Other Projects
==============

There are several open-source file encryption solutions for Linux available. In contrast
to disk-encryption software that operate on whole disks (TrueCrypt, dm-crypt etc), file
encryption operates on individual files that can be backed up or synchronised easily.

This page compares:

* [gocryptfs](https://nuetzlich.net/gocryptfs/) (this project), aspiring successor of EncFS
* [EncFS](https://github.com/vgough/encfs), mature with known security issues
* [eCryptFS](http://ecryptfs.org/), integrated into the Linux kernel
* [Cryptomator](https://cryptomator.org/), strong cross-platform support through Java and WebDAV

If you spot an error or want to see a project added, please
[file a ticket](https://github.com/rfjakob/gocryptfs-website)!

Overview
--------

|                     |        gocryptfs        |                encfs                 |           ecryptfs          |               cryptomator                |
| ------------------- | ----------------------- | ------------------------------------ | --------------------------- | ---------------------------------------- |
| First release       | 2015 [1]                | 2003 [2]                             | 2006 [3]                    | 2014 [4]                                 |
| Language            | Go                      | C++                                  | C                           | Java                                     |
| License             | MIT                     | LGPL/GPL [5]                         | GPL                         | MIT                                      |
| Development hotspot | Austria                 | USA                                  | UK (Canonical Ltd)          | Germany                                  |
| Lifecycle           | Active development      | Maintainance                         | Active development [9]      | Active development                       |
| File interface      | FUSE                    | FUSE                                 | in-kernel filesystem        | WebDAV                                   |
| Platforms           | Linux (OSX planned [7]) | Linux, OSX; third-party Windows port | Linux only                  | Linux, OSX, Windows                      |
| User interface      | Command line only       | Command line; third-party graphical  | Integrated in login process | Graphical only; Command line planned [8] |



References:
[[1]](https://github.com/rfjakob/gocryptfs/releases/tag/v0.1)
[[2]](https://github.com/vgough/encfs/blob/master/ChangeLog#L1442)
[[3]](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=237fead619984cc48818fe12ee0ceada3f55b012)
[[4]](https://github.com/cryptomator/cryptomator/releases/tag/v0.1.0)
[[5]](https://github.com/vgough/encfs/blob/master/COPYING)
[[6]](https://github.com/cryptomator/cryptomator/tree/master/LICENSES)
[[7]](https://github.com/rfjakob/gocryptfs/issues/15)
[[8]](https://github.com/cryptomator/cryptomator/issues/43)
[[9]](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/log/fs/ecryptfs)


General Security
----------------

|                         | gocryptfs | encfs default | encfs paranoia |               ecryptfs               | cryptomator |
| ----------------------- | --------- | ------------- | -------------- | ------------------------------------ | ----------- |
| Documentation available | Yes [1]   | Yes [2]       | Yes [2]        | No [4]                               | Yes [3]     |
| Password hashing        | scrypt    | PBKDF2        | PBKDF2         | (none, implemented in external tool) | scrypt      |


References:
[[1]](security.md)
[[2]](https://github.com/vgough/encfs/blob/master/DESIGN.md)
[[3]](https://cryptomator.org/#security)
[[4]](http://ecryptfs.org/documentation.html) actually, there is a lot of ecryptfs documentation, but none of
it seems to describe the used crypto.



File Contents
-------------

|                       | gocryptfs |      encfs default      |      encfs paranoia     |        ecryptfs       |      cryptomator       |
| --------------------- | --------- | ----------------------- | ----------------------- | --------------------- | ---------------------- |
| Encryption            | GCM       | CBC; last block CFB [1] | CBC; last block CFB [1] | CBC                   | CTR with random IV [2] |
| Integrity             | GCM       | none                    | HMAC                    | none                  | HMAC                   |
| File size obfuscation | no        | no                      | no                      | yes (4 KB increments) | yes (random padding)   |

References:
[[1]](https://github.com/vgough/encfs/issues/9)
[[2]](https://github.com/cryptomator/cryptomator/issues/128#issuecomment-168942517)

File Names
----------

|                         |       gocryptfs       |    encfs default     |    encfs paranoia    | ecryptfs | cryptomator |
| ----------------------- | --------------------- | -------------------- | -------------------- | -------- | ----------- |
| Encryption              | EME [4]               | CBC                  | CBC                  | CBC      | SIV         |
| Prefix leak             | no (EME)              | no (HMAC used as IV) | no (HMAC used as IV) | yes [2]  | no (SIV)    |
| Identical names leak    | no (per-directory IV) | no (path chaining)   | no (path chaining)   | yes [1]  | yes [3]     |
| Maximum name length [5] | 176                   | 176                  | 176                  | 144      | 1026        |

References:
[[1]](https://gist.github.com/rfjakob/a04364c55b3ee231078d)
[[2]](https://gist.github.com/rfjakob/61a17bf3c7eb9932d791)
[[3]](https://github.com/cryptomator/cryptomator/issues/128)
[[4]](https://github.com/rfjakob/eme)
[[5]](https://gist.github.com/rfjakob/c70344e2e7a1d765af1f)

Performance
-----------

All tests are run on tmpfs rule out any influence of the hard disk.
The CPU is an Intel Pentium G630 with 2 x 2.7GHz.

|                          | gocryptfs | encfs default | encfs paranoia | ecryptfs |  cryptomator  |
| ------------------------ | --------- | ------------- | -------------- | -------- | ------------- |
| Streaming write          | 84 MB/s   | 104 MB/s      | 56 MB/s        | 130 MB/s | 55 MB/s       |
| Extract linux-3.0.tar.gz | 48 s [4]  | 20 s          | 23 s           | 8.4 s    | 468 s [1] [2] |
| ls -lR linux-3.0         | 1.9 s     | 2.8 s         | 2.8 s          | 0.5 s    | 127 s [3]     |
| Delete linux-3.0         | 4.5 s     | 3.9 s         | 4.1 s          | 0.5 s    | 376 s [3]     |


Notes:

[1] All file acesses to cryptomator go through the WebDAV protocol, which is less performance-oriented than FUSE.
However, an optimized WebDAV client may be able to significantly speed up small-file workloads.

[2] Tested with the dave cli WebDAV client, which gave better speed than gvfs (Gnome built-in) and davfs2

[3] Tested with gvfs in the `/run/user/.../gvfs/dav:...` mount

[4] See [gocryptfs ticket#16](https://github.com/rfjakob/gocryptfs/issues/16)


Disk Space Efficiency
---------------------

(all file sizes in bytes)

|                      | gocryptfs | encfs default | encfs paranoia |  ecryptfs |      cryptomator      |
| -------------------- | --------- | ------------- | -------------- | --------- | --------------------- |
| Empty file           | 0         | 0             | 0              | 8,192     | 104 - 4,231           |
| 1 byte file          | 51        | 9             | 17             | 12,288    | 104 - 4,231           |
| 1,000,000 bytes file | 1,007,858 | 1,000,008     | 1,007,888      | 1,011,712 | 1,001,096 - 1,101,192 |
|                      |           |               |                |           |                       |

Note: cryptomator adds a random padding which is why the resulting size is non-deterministic.
