**1. CASE**

**1.1 CentOS Sanal Makinesi Vmware Workstation üzerine kurulur.**

  *  Oluşturulan kullanıcıda root yetkilisine geçilir.*
  
          sudo su
          
    Makineye güncelleştirme yapılır. Güncelleştirme yaparken soru sormaması için -y parametresi kullanılır.
    
          yum update -y
          
**1.2 Yeni kullanıcı eklenir ve kullanıcıya özel şifre belirlenir.**

			[root@localhost app01]# adduser sude.celen
			[root@localhost app01]# passwd sude.celen
			Changing password for user sude.celen.
			New password:
			Retype new password:
			passwd: all authentication tokens updated successfully.
      
**1.2.1 Bundan sonraki işlemleri rahatça yapabilmek için kullanıcıya root yetkisi verilir.**

			visudo
			..
			sude.celen ALL=(ALL)    ALL
			..
      
**1.3 Cihazın diski önce el ile manuel olarak arttırlır.**

    --Foto gelecek--
    
**1.3.1 Yeni disk eklenip eklenmediği kontrol edilir.**

			[sude.celen@localhost ~]$ lsblk
			NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
			sda      8:0    0   40G  0 disk
			├─sda1   8:1    0  300M  0 part /boot
			├─sda2   8:2    0    2G  0 part [SWAP]
			└─sda3   8:3    0 37.7G  0 part /
			sdb      8:16   0   10G  0 disk
			sr0     11:0    1 1024M  0 rom
      
**1.3.2 Diskin eklenmesi için 'fdisk' aracına geçilir.**

<pre>sudo fdisk /dev/sdb</pre>

	<pre>Command (m for help): p

	Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk label type: dos
	Disk identifier: 0x1f9238b6

	Device Boot      Start         End      Blocks   Id  System

	Command (m for help):
	</pre>

	<pre>Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): 1
	Invalid partition type `1&apos;

	Command (m for help): n
	Partition type:
	   p   primary (0 primary, 0 extended, 4 free)
	   e   extended
	Select (default p): p
	Partition number (1-4, default 1): 1
	First sector (2048-20971519, default 2048):
	Using default value 2048
	Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519):
	Using default value 20971519
	Partition 1 of type Linux and of size 10 GiB is set
	</pre>

**1.3.3 Diskin eklendiği kontrol edilir ve ayarlar kaydedilir.**

	Command (m for help): p

	Disk /dev/sdb: 10.7 GB, 10737418240 bytes, 20971520 sectors
	Units = sectors of 1 * 512 = 512 bytes
	Sector size (logical/physical): 512 bytes / 512 bytes
	I/O size (minimum/optimal): 512 bytes / 512 bytes
	Disk label type: dos
	Disk identifier: 0x1f9238b6

	   Device Boot      Start         End      Blocks   Id  System
	/dev/sdb1            2048    20971519    10484736   83  Linux

	Command (m for help): w
	The partition table has been altered!

	Calling ioctl() to re-read partition table.
	Syncing disks.

**1.3.4 Mount edilecek '/bootcamp' klasörü oluşturulur.**
**1.3.5 sdb1 formatlanır.**

      sudo mkfs.ext4 /dev/sdb1
      mke2fs 1.42.9 (28-Dec-2013)
      Filesystem label=
      OS type: Linux
      Block size=4096 (log=2)
      Fragment size=4096 (log=2)
      Stride=0 blocks, Stripe width=0 blocks
      655360 inodes, 2621184 blocks
      131059 blocks (5.00%) reserved for the super user
      First data block=0
      Maximum filesystem blocks=2151677952
      80 block groups
      32768 blocks per group, 32768 fragments per group
      8192 inodes per group
      Superblock backups stored on blocks:
      	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

      Allocating group tables: done
      Writing inode tables: done
      Creating journal (32768 blocks): done
      Writing superblocks and filesystem accounting information: done

**1.3.6 Mount işlemi yapılır ve kontrol edilir**

      [sude.celen@localhost /]$ sudo mount /dev/sdb1 /bootcamp
      [sude.celen@localhost /]$ df -h
      Filesystem      Size  Used Avail Use% Mounted on
      devtmpfs        1,9G     0  1,9G   0% /dev
      tmpfs           1,9G     0  1,9G   0% /dev/shm
      tmpfs           1,9G   13M  1,9G   1% /run
      tmpfs           1,9G     0  1,9G   0% /sys/fs/cgroup
      /dev/sda3        38G  5,2G   33G  14% /
      /dev/sda1       297M  196M  102M  66% /boot
      tmpfs           378M   20K  378M   1% /run/user/1001
      /dev/sdb1       9,8G   37M  9,2G   1% /bootcamp

**1.3.7 Mount işlemi bilgisayar yeniden başlatıldığında sıfırlanmaması için ayarlama yapılır ve kaydedilir..**

      sudo vi /etc/fstab

      Bu dizine girildikten sonra aşağıdaki şekilde ekleme yapılır.

      dev/sdb1     /bootcamp        ext4         defaults    0 0

      'sudo mount -a' ile kaydedilir.

**1.4 /opt/bootcamp/ altında bootcamp.txt diye bir file yaratılıp, file'ın içerisine "merhaba trendyol" yazılır.**

      [sude.celen@localhost /]$ mkdir -p /opt/bootcamp
      mkdir: cannot create directory ‘/opt/bootcamp’: Permission denied
      [sude.celen@localhost /]$ sudo mkdir -p /opt/bootcamp
      [sude.celen@localhost /]$ cd /opt/bootcamp
      [sude.celen@localhost bootcamp]$ sudo touch bootcamp.txt
      [sude.celen@localhost bootcamp]$ vim bootcamp.txt
      [sude.celen@localhost bootcamp]$ sudo vim bootcamp.txt

**1.5 Home dizininde tek bir komut kullanarak bootcamp.txt file'ını bulup, bootcamp
diskine taşınır**

	[root@localhost sude.celen] sudo find / -name bootcamp.txt -exec mv -t /bootcamp {} +
