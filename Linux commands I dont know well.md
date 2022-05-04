Linux commands I dont know well 

1. docker creates containers: docker pull [image], docker run [image](--restart=always),
   docker ps(shows running containers information),
   docker exec -it [container name] /bin/bash (this creates an interactive terminal for the docker container)

2. tar, gunzip, bzip2 xz 

df -h - disk free human readable 

3. nmtui - is a yum package that gives a network gui

4. history shows command history 

5. CREATING LXC container 
| $ sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
| yum install epel-release 
| yum install lxc lxc-templates lxc-extra 
|
| base dir is /usr/share/lxc 
| templates in /usr/share/lxc/templates 
|
| lxc-checkconfig 
|
| lxc-create -t download -n lxc-container1 (will ask questions then run the container)
| or 
| lxc-create -t centos -n lxc-centos1 (wont ask questions and run just the centos os)
| 
|
| systemctl start lxc-net -to create lxcbr0 bridge device
|
| lxc-ls -f -will list containers 
| lxc-start -n <containername>  -F will create an interactive shell 
| lxc-top
| lxc-attach -n <containername>
| lxc-stop <containername> 
|
| need to autostart lxc container in exam with lxc-autostart
| add lxc.start.auto = 1 in /var/lib/<containername>/config
| echo "lxc.start.auto = 1" >> /var/lib/<containername>/config 
________________________________


6. RAID
|
| lsblk - shows all mass storage devices and partitions
| mdadm package for RAID
| 
| use examples on man pages. 
| mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/hd[ac]1 
| 
| After setting up RAID. Next make filesystem on the RAID disk. 
| 
| mkfs.xfs /dev/md0 
|
| mkdir /raid 
|
| Edit fstab. vim /etc/fstab
| /dev/md0       /raid     xfs    defaults   0  0 
|
| mount -a (a is mount all that is not mounted right)
|
|
|
|
|
|
|
| RAID LEVELS
| 0. 2 disks. Improves performace and storage but no fault tolerance. 
|
| 1. 2 disks. Fault tolerance for one drive failure. Basically mirrored drives.
|
| 5. 3 disks. Highest read rate. Meduim write rate. One drive failure ok. 2 drive failure = data loss
|
| 10. 4 disks. Combination of level 0 and 1. Striping and mirrored. Data can be retrived as long as one drive is still funcitonal. 
|
|
|_________________________________________



7. LVM
|
| first need pysical volume. Either Disk, partition or RAID device 
|
| physical volume -> Volume group -> Logical volume
|
| must set partition type
|
| pvcreate 
| vgcreated 
| lvcreate
| mkfs
| mount/fstab 
| examn grades for surviving reboot. Must use fstab
|
| fdisk /dev/sd<unusedletter> 
| command m for help 
| n for new partition
| p for primary
| 1 for part number 
| default first sector 
| last sector +<amount>G
| p for print to view status
| t to change partition ID 
|           ^^^defualt partition ID is 8e
| w to write config 
| veryify it is usable by using lsblk
| 
| pvcreate /dev/<partition name>
| pvs will show all phyical volumes
| 
| vgcreate <volumegroupname> <devices> <devices>
| use man pages to see examples 
|
| vgs shows volume groups 
| vgdisplay detailed ^
|
| lvcreate -n <name> -L <size>G /dev/<volumegroup>
| lvs shows logical volumes  
|
| mkfs.ext4 /dev/v<volumegroup>/<logicalvolume>
| 
| finally put in fstab to survive boot
| /dev/<volumegroup>/<lvm>      /data    ext4   defaults   0 0 
|								^this is where it is suposed to be mounted 
| mkdir /data 
|	
| mount -a to mount all in fstab that hasnt been mounted yet
|
|_________________________________________________________________________________



8. extending volume groups and logical volumes 
| ext can be shrunk xfs cannot be shrunk 
|
| to extent logical volumes look at examples of lvextend man pages
|
| make new partition the size that you are trying to extend 
| partprobe 
| lsbllk to check if partition is available 
| pvcreate to create phyical volume 
| vgextendwill create phyical volume automatically 
| vgextend /dev/<volumegroupnamethatyouaretryingtoextend> /dev/<newphyicalgroup>
| lvextend -r(resize filesystem) -L +2G /dev/vgdata/lvdata to extend. Change names to you specific setting
|           ^^(can also use resize2fs)
|_________________________________________________________________________________________________


9. Find utility
|
| find / -name host
| * is wildcard 
|
| find / -user <users> - all files owed by user
|
| find / -size +100M - all files bigger than 100mb
|
| find / -perm /4000 - all files that have the set user id permissions 
| 
| -exec ls -l {} \;
|___________________________________________________________________________________________


10. groups/users/acls
|
| groupadd 
| mkdir -p(make parent dir as well) /data/<group>
| chgrp <group> <file> 
| chmod 1770 <file> or chmod +t <file> to make sticky bit
|
|
|
|
|
|
|
|
|
|