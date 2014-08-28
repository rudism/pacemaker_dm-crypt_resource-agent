To use, copy luksPartition to `/usr/lib/ocf/resource.d/luks/luksPartition`. Add a new resource like this:

    # pcs resource create luks_res ocf:luks:luksPartition device={DEVICE} mapped={MAPPED} keyfile={KEYFILE}

The parameters are as follows:

- **DEVICE** is the name of the encrypted device without the `/dev/` prefix. For example if your encrypted partition is `/dev/sdb1` then you wuld put `device=sdb1`.
- **MAPPED** is the name you want for the plaintext partition (the one you will actually mount to access the encrypted data). For example if you put `mapped=mydevice` then dm-crypt will create the partition `/dev/mapped/mydevice` for you to mount.
- **KEYFILE** is the path to your keyfile to open the LUKS partition. You should consider storing this on another encrypted partition that you manually open with a password and then mount on each node in the cluster prior to starting Pacemaker.

This agent does not mount the partition for you, you will need to add an `ocf:heartbeat:Filesystem` resource that points to the mapped device to do that for you. Make sure to set up an order constraint so that `luksPartition` always runs first.
