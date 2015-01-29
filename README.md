Arch Linux Installation
---

###PARTITION
	
	parted /dev/sdX
	mklabel gpt
	mkpart ESP fat32 1M 513M
	set 1 boot on
	mkpart primary btrfs 513M 100%

###MOUNT

	mount /dev/sda2 /mnt
	mkdir /mnt/boot
	mount /dev/sda1 /mnt/boot
	
###MIRRORLIST

	cp -vf /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
	pacman -Syy reflector
	reflector --verbose --country 'United States' -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist
	
###INSTALL
	
	nano /etc/pacman.conf
	
	Uncomment [multilib], then save.
	
	pacstrap -i /mnt base base-devel curl efibootmgr gawk git grep gzip lynx openssh reflector sed vim wget
	
###FSTAB

	genfstab -U -p /mnt >> /mnt/etc/fstab
	nano /mnt/etc/fstab
	
###CHROOT

	arch-chroot /mnt /bin/bash
	
###LOCALE

	nano /etc/locale.gen
	
	Uncomment en_US.UTF-8, save.
	
	locale-gen
	echo LANG=en_US.UTF-8 > /etc/locale.conf
	export LANG=en_US.UTF-8
	
###TIMEZONE

	ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
	hwclock --systohc --utc

###HOSTNAME

	echo hostname > /etc/hostname
	nano /etc/hosts
	
	Add hostname to /etc/hosts file.
	
###NETWORK

	systemctl enable dhcpcd.service

###ROOT

	passwd
	
###EFIBOOTMGR

	efibootmgr -d /dev/sda -p 1 -c -L "Arch Linux" -l /EFI/arch/vmlinuz-linux -u "root=/dev/sda2 rw quiet loglevel=3 rd.udev.log-priority=3 initrd=/EFI/arch/initramfs-linux.img"
	
###REBOOT

	exit
	umount -R /mnt
	reboot
	
Arch is installed. Uncomment [multilib] again.

###USER

	useradd -m -G wheel -s /bin/bash username
	chfn username
	passwd username
	visudo
	
	Allow group 'wheel' to execute commands.
	
	exit

###AURA

	wget https://aur.archlinux.org/packages/au/aura-bin/aura-bin.tar.gz
	tar xvzf aura-bin.tar.gz
	cd aura-bin
	makepkg -csi

###PACKAGES

Official - https://github.com/rilezfp/Keystone/blob/master/repository

AUR - https://github.com/rilezfp/Keystone/blob/master/AUR

	
	
	
