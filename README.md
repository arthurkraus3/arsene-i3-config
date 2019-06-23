# arsene-i3-config
i3 config originally created for ubuntu, but i'll be porting it over to my arch setup
sda1  1M bios boot ----None 
sda2 30G Linux Root (x8664) ----- mkfs.ext4 /dev/sda2 >> mount /dev/sda2 /mnt/
sda3 550M swap ---- mkswap /dev/sda3  swapon /dev/sda3
sda4 rest File system mkfs.ext4 /dev/sda4 > mkdir fs /dev/home mount fs /dev/home


#Installation for Arch Linux UEFI/GPT

#### Choose closest mirror list
>pacman -Sy
>pacman -S reflector
>reflector --verbose -l 5 --sort rate --save /etc/pacman.d/mirrorlist

####Install arch linux base packages

>pacstrap -i /mnt base base-devel

#### Configure Fstab and chroot /mnt

> genfstab -U -p /mnt >> /mnt/etc/fstab
> cat /mnt/etc/fstab
> arch-chroot /mnt

####Configure language and location

> nano /etc/locale.gen
****uncomment: en_US.UTF-8 UTF8 ****
> locale-gen
> echo LANG=en_US.UTF-8 > /etc/locale.conf
> export LANG=en_US.UTF-8

> mkinitcpio -p linux


####Set time zone

> ls /usr/share/zoneinfo
> ln -s /usr/share/zoneinfo/America/New_York > /etc/localtime
> ln -s /usr/share/zoneinfo/Region/City /etc/localtime

> hwclock --systohc --utc

####Configure the repository

> nano /etc/pacman.conf
****Uncomment the line: [multilib] and include = /etc/pacman.d/mirrorlist****

> pacman -Sy

####Set hostname and network

> echo linux-fanboy > /etc/hostname
> systemctl enable dhcpcd@enp0s3.service

####Set root password and create new user

> passwd
> pacman -S sudo bash-completion
> useradd -m -g users -G wheel,storage,power -s /bin/bash *usrname*
> passwd *password for usrname*

####Allow the users in wheel group to be able to preformance administrative tasks with sudo:

> EDITOR=nano visudo
****Uncomment the line: %wheel ALL=(ALL)****

####Install and configure bootloader


> pacman -S grub os-prober
> grub-install /dev/sda
> grub-mkconfig -o /boot/grub/grub.cfg

####Unmount the partitions and reboot

> exit
> umount -R /mnt
> reboot
