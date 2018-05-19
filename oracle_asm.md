## Oracle ASM

### mknod approach (old)

Device files are created in `/dev` using `mknod /dev/blah c maj min` to have
the same major and minor number as the disk device to be used. The Oracle DBA will
use these device names to provide a certain amount of device independance.

If Oracle ASM is using raw disks & they are not in LVM,
then storage based replication is the best way to migrate, where available.
But this means the device minor and major numbers are likely to change.

ASM devices have a header which contains an asm id. To extract, do:

    dd if=/dev/$disk bs=1 skip=72 count=32 2>/dev/null

These ids can be used to map the and the new devices and therefore
create new asm device files which point to the correct, new disks.

### rendev / lkdev approach (new)

Rationale: An ASM disks has no pvid and so looks like it's unassigned.
An AIX admin can therefore mistakenly think the disk is free and add it
to a volume group, thus destroying data.

Use rendev to rename ASM hdisks to something more obviously ASM, e.g.
hdiskasm5, and if necessary update the Oracle ASM device scan path.  Also,
lkdev can be used as an extra level of protection. The ASM header name can also
be added as a comment when using lkdev, to make it even more obvious.

