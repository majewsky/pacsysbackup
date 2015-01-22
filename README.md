pacsysbackup
============

`pacsysbackup` generates a compact system backup by diff'ing the Pacman
database against the system partitions. Its output is a tarball containing

* the output of `pacman -Q`: a list of all installed packages and their versions
* all files (and directories) that are not contained in any packages, or have
  been changed from the version included in their package

Under the assumption that the original package files (or their repositories)
will still be available in the future, this is all the information that you
need to fully restore the system state.

What is NOT included in the backup?
-----------------------------------

`pacsysbackup` backs up system data only, not user data. User data is

1. everything below /home
2. all partitions mounted in non-standard locations (see below)

Furthermore, data on volatile virtual filesystems such as `/dev`, `/proc` or
`/run`, is also skipped. We only include data that are stored on actual
blockdevices.

*Non-standard locations* are those not defined by the [Filesystem Hierarchy
Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard). For
example, if you have your `/usr` on a separate partition, it will be considered
a system partition because `/usr` is a standard directory. If you also have a
partition on `/data`, that will be considered user data, because the FHS does
not define `/data`.

I strongly advise that you check how `pacsysbackup` classifies your filesystems
when you set up your backup strategy. The `--verbose` output can be used for
this purpose. Here's how it looks on my machine:

```
$ sudo pacsysbackup --verbose
Scanning filesystems...
        [virtual FS] /proc
        [virtual FS] /sys
        [virtual FS] /dev
        [virtual FS] /run
        [syst. data] /
        [virtual FS] /sys/kernel/security
        [virtual FS] /dev/shm
        [virtual FS] /dev/pts
        [virtual FS] /sys/fs/cgroup
        [virtual FS] /sys/fs/cgroup/systemd
        [virtual FS] /sys/fs/pstore
        [virtual FS] /sys/firmware/efi/efivars
        [virtual FS] /sys/fs/cgroup/cpuset
        [virtual FS] /sys/fs/cgroup/blkio
        [virtual FS] /sys/fs/cgroup/cpu,cpuacct
        [virtual FS] /sys/fs/cgroup/freezer
        [virtual FS] /sys/fs/cgroup/net_cls
        [virtual FS] /sys/fs/cgroup/memory
        [virtual FS] /sys/fs/cgroup/devices
        [virtual FS] /sys/kernel/debug
        [virtual FS] /proc/sys/fs/binfmt_misc
        [virtual FS] /dev/hugepages
        [virtual FS] /dev/mqueue
        [virtual FS] /tmp
        [virtual FS] /sys/kernel/config
        [user  data] /home
        [virtual FS] /run/user/995
        [virtual FS] /run/user/1001
Found 1 system partitions (/)
...
```

TODO
====

describe how to use the program, once it is fully implemented :)
