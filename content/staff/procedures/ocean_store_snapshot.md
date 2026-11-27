---
title: Recovery From OceanStore's Snapshots
---

This is the procedure to follow to recover OceanStore's Snapshots.
Snapshots are preserved in a `.snapshot` folder within the directory that is snapshotted;

An example is for the user's homes the snapshot are taken for the folder `/home_trantor` which contains 
the folder `user_homes` that is what is actually mounted in `/home`; in this case the snapshots are
located in `/home_trantor/.snapshot` and are therefore not visible in the `/home` mountpoint.

To recover the snapshots, the directory should be mounted, normally we do that from `terminus`:
```shell
mount default.dyn-ocean:/home_trantor /mnt/snapshots/
```

In the folder you can find all the available snapshots with a date tag:
```shell
ls /mnt/snapshots/.snapshot/
/mnt/snapshots/.snapshot/homes_daily_snapshot_auto_YYYY-MM-DD_HH-MM-SS/users_homes/
```

Using `rsync -Paz` or similar we copy the required files into a folder we generally call `_BACKUP` 
in the user's home.
