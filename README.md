# ZFS-for-large-db-storage

### Creating a ZFS database using AWS ubuntu spot instance.

#### Install ZFS

```
sudo apt-get install zfs
```

#### Allocate the size of storage you wish to use

```
fallocate -l 6G /home/ubuntu/zfsimage.img
```

or

```
dd if=/dev/zero of=/home/ubuntu/zfsimage.img bs=1M count=6000
```

Or even a sparse image (100GB image)

```
dd if=/dev/zero of=/home/ubuntu/zfsimage.img bs=1k count=1 seek=100M
```

#### Create the zpool

```
sudo zpool create database /home/ubuntu/zfsimage.img
```

Now you can see your zpool.

```
ubuntu@ip-172-31-35-70:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            7.5G     0  7.5G   0% /dev
tmpfs           1.5G  8.6M  1.5G   1% /run
/dev/xvda1      7.8G  7.0G  405M  95% /
tmpfs           7.5G     0  7.5G   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           7.5G     0  7.5G   0% /sys/fs/cgroup
tmpfs           1.5G     0  1.5G   0% /run/user/1000
database        5.8G     0  5.8G   0% /database
ubuntu@ip-172-31-35-70:~$ sudo zpool status
  pool: database
 state: ONLINE
  scan: none requested
config:

	NAME                         STATE     READ WRITE CKSUM
	database                     ONLINE       0     0     0
	  /home/ubuntu/zfsimage.img  ONLINE       0     0     0

errors: No known data errors
```

#### Set compression

```
ubuntu@ip-172-31-35-70:~$ sudo zfs set compression=lz4 database
ubuntu@ip-172-31-35-70:~$ sudo zfs get compressratio database
NAME      PROPERTY       VALUE  SOURCE
database  compressratio  1.00x  -
```

#### Set deduplication

```
ubuntu@ip-172-31-35-70:~$ sudo zfs set dedup=on database
ubuntu@ip-172-31-35-70:~$ sudo zfs get compressratio database
```
