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
	- Uncomment [multilib], then save.
	- pacstrap -i /mnt base base-devel git wget openssh
	
###Generate fstab:

	- genfstab -U -p /mnt >> /mnt/etc/fstab
	- nano /mnt/etc/fstab (Make sure your partitions are there.)
	
###Chroot:

	- arch-chroot /mnt /bin/bash
	
###Locale:

	- nano /etc/locale.gen
	- Uncomment en_US.UTF-8, save.
	- locale-gen
	- echo LANG=en_US.UTF-8 > /etc/locale.conf
	- export LANG=en_US.UTF-8
	
###Time:

	- ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
	- hwclock --systohc --utc

###Hostname:

	- echo keystone > /etc/hostname
	
###Network:

	- systemctl enable dhcpcd.service (Disable this after installing NetworkManager)

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
	
13 steps for a basic installation of Arch. When it's done, you will need to uncomment [multilib] again (nano /etc/pacman.conf). After that, set up your user account, and install Aura for package management:

###User

	- useradd -m -g users -G wheel -s /bin/bash usernamehere
	- chfn usernamehere
	- passwd usernamehere
	- nano /etc/sudoers
	- Uncomment, allow group 'wheel' to execute commands. Save.
	- exit
	- Continue installing packages with your user account.

###Aura

	- wget https://aur.archlinux.org/packages/au/aura-bin/aura-bin.tar.gz
	- tar xvzf aura-bin.tar.gz
	- cd aura-bin
	- makepkg -csi
	
Aura supports the same modifiers as pacman (-S, -Syyu, -Rsc, etc) but also supports the AUR (-A) and package downgrades (-B and -Br).

###Font Rendering

	- gedit /etc/pacman.conf
	- Add the following repositories:
	
	[infinality-bundle]
	Server = http://bohoomil.com/repo/$arch

	[infinality-bundle-multilib]
	Server = http://bohoomil.com/repo/multilib/$arch

	[infinality-bundle-fonts]
	Server = http://bohoomil.com/repo/fonts
	
	- sudo pacman-key -r 962DDE58
	- sudo pacman-key --lsign-key 962DDE58
	- sudo aura -Syyu
	- sudo aura -S infinality-bundle infinality-bundle-multilib ibfonts-meta-base ibfonts-meta-extended
	
	Note: We are still testing these patched libraries. Default font rendering might be sufficient, but fonts won't look right by default. If you're not using Infiniality, you will need to configure your fonts. Refer here for now:

	https://wiki.archlinux.org/index.php/Font_configuration

###Install packages
	
	- Back up your packages. (sudo aura -B)
	- Repositories (sudo aura -S): https://github.com/rilezfp/Keystone/blob/master/repository
	- AUR (sudo aura -A): https://github.com/rilezfp/Keystone/blob/master/AUR
	- Infiniality specific fonts: https://github.com/rilezfp/Keystone/blob/master/infinality

	
	
	
