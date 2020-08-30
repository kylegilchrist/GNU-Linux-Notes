# GNU-Linux-Notes
Arch Linux Install
reflector —-verbose —-latest 50 —-sort rate -c US(—-save /etc/pacman.d/mirrorlist) 
pacman -Syyy

gdisk /dev/sdX
	o
	n ef00 (boot)
	n 8e00 (lvm)
	w
mkfs.fat -F32 /dev/sdX1


cryptsetup luksFormat /dev/sdX2
cryptsetup open —-type=luks /dev/sdX2 lvm

pvcreate —-dataalighment 1m /dev/mapper/lvm

vgcreate vg0 /dev/mapper/lvm

lvcreate -L30G vg0 -n lvroot
lvcreate -L100G vg0 -n lvhome

modprobe dm_mod
vgscan
vgchange -ay

mkfs.ext4 /dev/vg0/lvroot
mkfs.ext4 /dev/vg0/lvhome

mount /dev/vg0/lvroot /mnt

mkdir /mnt/boot
mount /dev/sdX1 /mnt/boot

mkdir /mnt/home
mount /dev/vg0/lvhome /mnt/home
mount

mkdir /mnt/etc
genfstab -U -p /mnt >> /mnt/etc/fstab

pacstrap -i /mnt base linux linux-firmware

arch-chroot

pacman -S base-devel  linux-headers lvm2 dialog vim 
amd-ucode networkmanager wireless_tools  wpa_supplicant xf86-video-amdgpu	
systemctl enable NetworkManager


vim /etc/mkinitcpio.conf
	(after block add) keyboard encrypt lvm2
mkinitcpio -p linux

ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
hwclock -w —u  (maybe -l if dual booting windows)

passwd
useradd -m -g users -G wheel black
passwd black

EDITOR=vim visudo
	(uncomment the group wheel to execute any command)	
vim /etc/locale.gen
	uncomment en_US.UTS-8
locale-gen
locale > /etc/locale.conf

vim /etc/hostname

bootctl —-path=/boot install

vim /boot/loader/loader.conf
	default arch
	timeout 3
	editor 0

blkid | grep crypto >> /boot/loader/entries/arch.conf
vim /boot/loader/entries/arch.conf
	title Arch Linux
	linux /vmlinuz-linux
	initrd /amd-ucode	.img
	initrd /initramfs-linux.img
	options cryptdevice=UUID=[the UUID]:vg0:allow-discards root=/dev/mapper/vg0-lvroot quiet rw

vim  /etc/pacman.conf 
	enable multilib 

fallocate -l 2G /swapfile
mkswap /swapfile
chmod 600 /swapfile
echo ‘/swapfile none swap sw 0 0’ >> /etc/fstab
mount -a
exit

unmount -a
reboot










crytsetuo open —-type=luks /dev/sdb2 lvm
mount /dev/vg0/lvroot /mnt
mount /dev/sdX1 /mnt/boot
mount /dev/vg0/lvhome /mnt/home




#Verify how much space you have left
vgs

#Create the snapshot
lvcreate -L 5GB -s -n root_snapshot_20190526 /dev/mapper/volgroup0-lv_root

#List current snapshots
 lvs

#Snapshot restore command
lvconvert --merge /dev/volgroup0/root_snapshot_20190526

#To make a snapshot final, remove it
lveremove /dev/volgroup0/root_snapshot_20190526


sudo pacman -S zsh zsh-completions grml-zsh-config man-db man-pages
chsh -s /usr/bin/zsh

    pulseaudio-alsa pulseaudio-bluetooth pulseaudio-equalizer pavucontrol
   
waybar wofi mako neofetch wal



