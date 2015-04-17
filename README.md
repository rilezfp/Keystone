Arch Linux Installation
---

###PARTITION
	
	parted /dev/sdX
	mklabel gpt
	mkpart ESP fat32 1M 513M
	set 1 boot on
	mkpart primary btrfs 513M 100%
	mkfs.vfat -F32 /dev/sda1
	mkfs.btrfs /dev/sda2

###MOUNT

	mount /dev/sda2 /mnt
	mkdir /mnt/boot
	mount /dev/sda1 /mnt/boot
	
###INSTALL
	
	nano /etc/pacman.conf
	Uncomment [multilib]
	nano /etc/pacman.d/mirrorlist
	Select mirror of choice
	pacstrap -i /mnt base base-devel curl efibootmgr git openssh reflector vim wget
	
###FSTAB

	genfstab -U -p /mnt >> /mnt/etc/fstab
	nano /mnt/etc/fstab
	
###CHROOT

	arch-chroot /mnt /bin/bash
	
###LOCALE

	nano /etc/locale.gen
	Uncomment en_US.UTF-8
	locale-gen
	echo LANG=en_US.UTF-8 > /etc/locale.conf
	export LANG=en_US.UTF-8
	
###TIMEZONE

	ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
	hwclock --systohc --utc

###HOSTNAME

	echo hostname > /etc/hostname
	nano /etc/hosts
	
###NETWORK

	systemctl enable dhcpcd.service

###ROOT

	passwd
	
###EFIBOOTMGR

	efibootmgr -d /dev/sda -p 1 -c -L "Arch Linux QEMU" -l vmlinuz-linux -u "root=/dev/sda2 rw quiet loglevel=0 rd.udev.log-priority=3 intel_iommu=on pci-stub.ids=10de:1189,10de:0e0a initrd=initramfs-linux.img"
	
	Arch is installed.

###USER

	useradd -m -G wheel -s /bin/bash username
	chfn username
	passwd username
	visudo
	exit

###MIRRORLIST

	cp -vf /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
	pacman -Syy reflector
	reflector --verbose --country 'United States' -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist

###AURA

	wget https://aur.archlinux.org/packages/au/aura-bin/aura-bin.tar.gz
	tar xvzf aura-bin.tar.gz
	cd aura-bin
	makepkg -csi
	
###MKINITCPIO

	nano /etc/mkinitcpio.conf
	Add pci-stub to modules, remove fsck from hook.
	Copy the files systemd-fsck-root.service and systemd-fsck@.service located at /usr/lib/systemd/system/ to /etc/systemd/system/
	StandardOutput=null
	StandardError=journal+console
	mkinitcpio -p linux
	
###REBOOT

	exit
	umount -R /mnt
	reboot

###PACKAGES

Official - https://github.com/rilezfp/Keystone/blob/master/repository
AUR - https://github.com/rilezfp/Keystone/blob/master/AUR

Disable dhcpcd and setup NetworkManager.service + bridge0

	
	
