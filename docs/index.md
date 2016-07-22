gocryptfs - simple. secure. fast.
=================================

gocryptfs uses file-based encryption that is implemented as a mountable
FUSE filesystem.
Each file in gocryptfs is stored one corresponding encrypted file on
the hard disk. The
screenshot below shows a mounted gocryptfs filesystem (left) and the
encrypted files (right).

The encrypted files can be stored in any folder on your hard disk, a USB
stick or even inside the Dropbox folder. One advantage of file-based
encryption as opposed to disk encryption is that encrypted files can
be synchronised efficiently using standard tools like Dropbox or rsync.
Also, the size of the encrypted filesystem is dynamic and only limited
by the available disk space.

![](img/folders-side-by-side.jpg)

This project was inspired by EncFS and strives to fix its security
issues while providing good performance,
see the [Comparison](comparison#performance) page for benchmarks.

The [Security](security) page details gocryptfs's cryptographic design.
The highlights are: Scrypt password hashing, GCM encryption for all
file contents, EME wide-block encryption for file names with a per-directory
IV.

gocryptfs has reached version 1.0 on Jul 17, 2016. It has gone through
hours and hours of stress (fsstress, extractloop.bash) and correctness
testing (xfstests). It is now considered ready for general consumption.

The old principle still applies: Important data should have a backup.
Also, keep a copy of your master key (printed on mount) in a safe place.
This allows you to access the data even if the gocryptfs.conf config
file is damaged or you lose the password.

Only Linux is fully supported at the moment. OSX support is experimental
but seems to mostly work. please chime in in the
["Mac OS X support" ticket](https://github.com/rfjakob/gocryptfs/issues/15)
if you are interested.

gocryptfs is, and always will be, free software.
