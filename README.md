Arch Linux Installation
---

###1. Partition:
	
	- parted /dev/sdX
	- mklabel gpt
	- mkpart ESP fat32 1M 513M
	- set 1 boot on
	- mkpart primary btrfs 513M 100%

###2. Mount:

	- mount /dev/sda2 /mnt
	- mkdir /mnt/boot
	- mount /dev/sda1 /mnt/boot
	
###3. Sort mirrorlist:

	- cp -vf /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.backup
	- pacman -Syy reflector
	- reflector --verbose --country 'United States' -l 200 -p http --sort rate --save /etc/pacman.d/mirrorlist
	
###4. Install:
	
	- nano /etc/pacman.conf
	- Uncomment [multilib], then save.
	- pacstrap -i /mnt base base-devel curl efibootmgr gawk git grep gzip lynx openssh reflector sed vim wget
	
###5. Generate fstab:

	- genfstab -U -p /mnt >> /mnt/etc/fstab
	- nano /mnt/etc/fstab (Make sure your partitions are there.)
	
###6. chroot:

	- arch-chroot /mnt /bin/bash
	
###7. Set locale:

	- nano /etc/locale.gen
	- Uncomment en_US.UTF-8, save.
	- locale-gen
	- echo LANG=en_US.UTF-8 > /etc/locale.conf
	- export LANG=en_US.UTF-8
	
###8. Set timezone:

	- ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
	- hwclock --systohc --utc

###9. Set hostname:

	- echo keystone > /etc/hostname
	
###10. Enable network:

	- systemctl enable dhcpcd.service

###11. Set root password:

	- passwd
	
###12. Enable efibootmgr:

	- efibootmgr -d /dev/sda -p 1 -c -L "Arch Linux" -l /EFI/arch/vmlinuz-linux -u "root=/dev/sda2 rw quiet loglevel=3 rd.udev.log-priority=3 initrd=/EFI/arch/initramfs-linux.img"
	
###13. Reboot:

	- exit
	- umount -R /mnt
	- reboot
	
Arch is installed. Uncomment [multilib] again. Then:

###Create user account

	- useradd -m -G wheel -s /bin/bash username
	- chfn username
	- passwd username
	- visudo
	- Allow group 'wheel' to execute commands.
	- exit
	- Continue installing packages with your user account.

###Aura

	- wget https://aur.archlinux.org/packages/au/aura-bin/aura-bin.tar.gz
	- tar xvzf aura-bin.tar.gz
	- cd aura-bin
	- makepkg -csi

###Install packages

Repositories (sudo aura -S): https://github.com/rilezfp/Keystone/blob/master/repository

AUR (sudo aura -A): https://github.com/rilezfp/Keystone/blob/master/AUR

Infiniality specific fonts: https://github.com/rilezfp/Keystone/blob/master/infinality

	
	
	
