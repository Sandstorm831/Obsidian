- [[Omarchy]]
- [[autoinstall]]
- [[xorisso]]
- [[systemd]]
- [[useradd]]

| Common Filesystems | Mount tool or command |
| :----------------: | --------------------: |
| NTFS, FAT32, exFAT |    `ntfs-3g`, `mount` |
|       FAT32        |               `mount` |
|  ext4, xfs, btrfs  |               `mount` |
- `disks` and `partitions` in the Linux represented as special files, and those files live in the `/dev/` directory -> short for `devices`


| signals | COMMAND equivalents |
| ------- | ------------------- |
| SIGINT  | Ctrl+C              |
| SIGTERM | `kill <pid>`        |
| SIGKILL | `kill -9 <pid>`     |
