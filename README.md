Keystone Linux: Installing Arch
---

###Partition/Format Drive:
	
  	-cgdisk /dev/sda
		- N -> enter -> 512M -> enter -> ef00 -> enter (efi)
		- N -> enter -> enter -> enter (root)
	- mkfs.fat -F32 /dev/sda1
	- mkfs.btrfs /dev/sda2

###Mount Drive:

	- mount /dev/sda2 /mnt
	- mkdir /mnt/home
	- mount /dev/sda2 /mnt/home
	- mkdir /mnt/boot
	- mount /dev/sda1 /mnt/boot
	
###Set mirrors:

	- pacman -Syy reflector
	- reflector --verbose --country 'United States' -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist
	
###Install:
	
	- nano /etc/pacman.conf
	- uncomment multilib
	- pacstrap -i /mnt base base-devel git wget openssh
	
###Generate fstab:

	- genfstab -U -p /mnt >> /mnt/etc/fstab
	- nano /mnt/etc/fstab (to check)
	
###Chroot:

	- arch-chroot /mnt /bin/bash
	
###Locale:

	- nano /etc/locale.gen
	- uncomment en_US.UTF-8
	- locale-gen
	- echo LANG=en_US.UTF-8 > /etc/locale.conf
	- export LANG=en_US.UTF-8
	
###Time:

	- ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
	- hwclock --systohc --utc

###Hostname:

	- echo keystone > /etc/hostname
	
###Network:

	- systemctl enable dhcpcd.service (remember to disable after installing NetworkManager)

###Root Password:

	- passwd
	
###GRUB:

	- pacman -S grub efibootmgr
	- grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=Keystone --recheck
	- grub-mkconfig -o /boot/grub/grub.cfg
	
###Reboot:

	- exit
	- umount -R /mnt
	- reboot
	
13 steps for a base installation of Arch.
