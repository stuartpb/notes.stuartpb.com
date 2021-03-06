# Revisiting Automounting Partitions One Last Time for the Longhorn Thing

So, I had a way that I set up partitions for Longhorn as disks

and I think we could go on with a similar model

we should make a new UUID anyway because, even though it's a minor change and we're the only ones it affects and the only machine that ever followed it is gone, the old configs are still out there, and it's not like UUIDs cost money

## insight into systemd's automounting

[the docs](https://www.freedesktop.org/software/systemd/man/systemd-mount.html) aren't terribly clear (emphasis mine):

> The command takes either one or two arguments. If only one argument is specified it should refer to a block device or regular file containing a file system (e.g. "`/dev/sdb1`" or "`/path/to/disk.img`"). **The block device or image file is then probed for a file system label and other metadata, and is mounted to a directory below `/run/media/system/` whose name is generated from the file system label.** In this mode the block device or image file must exist at the time of invocation of the command, so that it may be probed. If the device is found to be a removable block device (e.g. a USB stick) an automount point instead of a regular mount point is created (i.e. the `--automount=` option is implied, see below).

(incidentally, this is why I recommend using non-word variable names: Google's results for a quoted "/run/state/media" after that docs page are all news articles about Turkey)

Can't remember how I caught wind of this (I think I looked for files that included the `SYSTEMD_MOUNT_WHERE` string), but [here's](https://github.com/systemd/systemd/blob/c15ab81ed9fa9437fdc31b6761ad331f6fd52400/src/mount/mount-tool.c#L1104-L1126) the default logic, from what I can tell:

- It gets the label for the partition
- If there's no label, it gets the model
- If it can't get the model, it

anyway, all of these defaults are under `/run/state/media`, so that's where I'll put the v2 mounts - rather than mess with the label magic, I'll have the default for the partition type to be mounted by partition UUID

## anyway, we can do mounts better

we're probably gonna want to do a different partition model anyway. It looks like lvm doesn't even use the mount system, or at least, it doesn't use those commands.

what'd probably end up making the most sense would be to make sure that the presence of all lvm volumes result in mappings for all data pools their LVM supports the "storage class" for

## understanding LVM enough to bootstrap this

Let's start with a high-ranked Google result that's not over a decade old: https://www.digitalocean.com/community/tutorials/an-introduction-to-lvm-concepts-terminology-and-operations

so a "physical volume" is a construct that appears to the system as a hardware block device, like, you know, an actual drive

a "volume group" is a pool of these, to be used according to the definition in...

a "logical volume". which, for our purposes, basically dictates how we're going to translate this "volume group" into a usable hardware setup for the local volume construction layer, which is Longhorn (which handles cross-node clustering). We can even make separate classes of storage under the same volume-group pool, I suppose

okay, so I guess I can set this up inside of a partition, but this actually suggests just going ahead and making the whole disk a physical volume? Can't argue, TBH

so I think we'll have "volume groups" created at the cluster level at some point? and mirrored out to each node

and nodes can override this maybe? like if on one node I want the volume to have a higher striping factor because there are more CPUs or whatever

and I'm guessing nodes auto-identify to the volume group?

## another tutorial

https://documentation.suse.com/sles/15-SP1/html/SLES-all/cha-lvm.html

## anyway

for now, I don't really have enough storage devices to get clever with a "raid" setup. I might order some bulk MicroSD cards and get them in a RAID6?

but yeah, anyway, the bottom line is, I'm not really going to use LVM to try to emulate SATA / Cluster RAID any time soon: I have Longhorn for that.

I am looking at hardware, though: [Browsing for ideas for cluster storage array construction](eb3nf-b5ycy-rc8zs-j2h4q-0x28k)
