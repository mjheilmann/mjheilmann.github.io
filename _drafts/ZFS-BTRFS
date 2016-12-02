ZFS on ubuntu 16.04
-> working very well, but several times per day it will hang my workstation for tens of seconds

Lets switch to BTRFS, without a backup! (this is on a backup device, so the original still exists)

I have a raidz1-0 on 4 devices
 - mirror vdevs would be much easier, as we could just degrade the pairs and use the freed disks to start the new array

I have instead a raidz1 - online sources say to rsync it off somewhere, rebuild from scratch, then rsync it back.  I have 3.8 TB of data, lets do this in-place
luckily I didnt enable compression on zfs, so maybe... just maybe...

1 - degrade zfs pool by removing disk
 - lets offline it - nope, zfs doesnt identify this disk for us
 - format the disk
	apt install btrfs-tools
	sudo mkfs.btrfs -f /dev/sdd
	sudo mount -o compression /dev/sdd /enclosure
 - rsync files accross (slow, but much faster as local sync than remote)
	lots of waiting ...
	- trim some transient data, it will be rolled over on next bakcup sync anyways
	- still not fitting.  Lucky its a backup! skip what doesn't fit, and move on.  We will update the backup later
 - format remaining disks
	unmount zfs volume
	zpool destroy VOLUME
 - expand btrfs volume
	 btrfs device add -f /dev/sde /enclosure
 - backups should be safe, change raid level from 0 to 1 (raid 5/6 is not stable for production, and this should be lots of space)
	btrfs balance start -dconvert=raid1 -mconvert=raid1 /CineRAID
 - wahoo
