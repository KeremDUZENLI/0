# 1 Pre-installation
## 1.5 Set the console keyboard layout and font
```bash
loadkeys trq
setfont lat2-16 -m 8859-9
```

## 1.6 Verify the boot mode
```bash
ls /sys/firmware/efi
```

## 1.7 Connect to the internet
```bash
ip addr show

iwctl
station wlan0 scan
station wlan0 get-networks
station wlan0 connect "SSID"
<Password>
exit

ping -c 3 archlinux.org
```

## 1.8 Update the system clock
```bash
timedatectl set-timezone Europe/Budapest
```

## 1.9 Partition the disks
```bash
lsblk -f

sgdisk --zap-all /dev/sdX  # Removes GPT + MBR tables only
wipefs --all /dev/sdX      # Removes RAID/LVM signatures
```

##### parted (MBR|GPT)
```bash
parted /dev/sdX --align optimal
> mklabel msdos|gpt
> mkpart primary ext4|fat32 1MiB 513MiB
> set 1 boot on
> mkpart primary linux-swap 513MiB 2561MiB  # Optional
> mkpart primary ext4 2561MiB 100%
> quit

parted /dev/sdY --align optimal
> mklabel msdos|gpt
> mkpart primary ntfs 1MiB 100%
> quit
```

##### fdisk(MBR) | gdisk(GPT)
```bash
fdisk /dev/sdX
> p (print partition table)
> o (mbr) | g (gpt)

-----/mnt/boot-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector: +512M
> t (type)
> 83 (Linux)

-----/mnt-----
> n (new partition)
> Partition number: 2
> First sector:
> Last sector:
> t (type)
> 83 (Linux)

-----swap-----  # Optional
> n (new partition)
> Partition number: 3
> First sector:
> Last sector: +2G
> t (type)
> 82 (Linux swap)

> w


fdisk /dev/sdY
> p (print partition table)
> o (mbr) | g (gpt)

-----/mnt/SharedDisk-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector:
> t (type)
> 07 (NTFS)

> w
```

##### Encryption (optional)
```bash
fdisk /dev/sdX
> p (print partition table)
> g (gpt)

-----/boot/EFI-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector: +1G

-----/mnt/boot-----
> n (new partition)
> Partition number: 2
> First sector:
> Last sector: +1G

-----/mnt (volgroup0)-----
> n (new partition)
> Partition number: 3
> First sector:
> Last sector:

> t (type)
> 44 (Linux LVM)

> w
```


## 1.10 Format the partitions
```bash
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2
mkswap /dev/sdX3

mkfs.ntfs -f /dev/sdY1
```

##### Encryption (optional)
```bash
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2
cryptsetup luksFormat /dev/sdX3

cryptsetup open --type luks /dev/sdX3 lvm
pvcreate /dev/mapper/lvm
vgcreate volgroup0 /dev/mapper/lvm
lvcreate -L 30GB volgroup0 -n lv_root
lvcreate -L 100%FREE volgroup0 -n lv_home

vgdisplay | lvdisplay

modprobe dm_mod
vgscan
vgchange -ay

mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
```


## 1.11 Mount the file systems
```bash
mount /dev/sdX2 /mnt
mount --mkdir /dev/sdX1 /mnt/boot
swapon /dev/sdX3

mount --mkdir -t ntfs3 /dev/sdY1 /mnt/SharedDisk

!Note: To see all mounted parts use "findmnt"
```

##### Encryption (optional)
```bash
mount /dev/volgroup0/lv_root /mnt
mount --mkdir /dev/volgroup0/lv_home /mnt/home

mount --mkdir /dev/sdX2 /mnt/boot
```

# 2 Installation
## 2.1 Select the mirrors
```bash
reflector --country Hungary,Austria,Germany --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist

less /etc/pacman.d/mirrorlist
```

## 2.2 Install essential packages
```bash
pacstrap -K /mnt base linux linux-firmware networkmanager nano
```

# 3 Configure the system
## 3.1 Fstab
```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
nano /mnt/etc/fstab
> UUID=XXXX-XXXX  /SharedDisk  ntfs3  defaults,noatime  0  0
```

## 3.2 Chroot
```bash
arch-chroot /mnt
```

## 3.3 Time
```bash
ln -sf /usr/share/zoneinfo/Europe/Budapest /etc/localtime
hwclock --systohc
```

## 3.4 Localization
```bash
nano /etc/locale.gen
> en_US.UTF-8 UTF-8
locale-gen

nano /etc/locale.conf
> LANG=en_US.UTF-8

nano /etc/vconsole.conf
> KEYMAP=tr_q
> FONT=lat2-16
> FONT_MAP=8859-9

localectl set-keymap --no-convert tr_q
localectl set-x11-keymap tr qwerty
locale
localectl status
```

## 3.5 Network configuration
```bash
echo HostName > /etc/hostname

nano /etc/hosts
> 127.0.0.1   localhost
> ::1         localhost
> 127.0.1.1   HostName.localdomain HostName

systemctl enable NetworkManager
```

## 3.6 Initramfs
##### Packages
```bash
pacman -S linux-headers dosfstools mtools sudo git base-devel openssh os-prober lvm2
```

##### Graphic Driver
```bash
lspci | grep -E "VGA|3D"
pacman -S mesa                  # OpenGL/Vulkan for Intel & AMD
pacman -S intel-media-driver    # VA‑API for Intel GPUs
pacman -S libva-mesa-driver   	# VA‑API for AMD GPUs
pacman -S nvidia nvidia-utils 	# NVIDIA proprietary drivers

# For old Intel drivers
pacman -S xf86-video-intel

# For old NVIDIA drivers
nano /etc/pacman.conf
> [multilib]
> Include = /etc/pacman.d/mirrorlist
pacman -Sy
pacman -S lib32-zlib lib32-libglvnd

cd ~
git clone https://aur.archlinux.org/nvidia-470xx-dkms.git
cd nvidia-470xx-dkms
makepkg -si

cd ~
git clone https://aur.archlinux.org/lib32-nvidia-470xx-utils.git
cd lib32-nvidia-470xx-utils
makepkg -si

nvidia-xconfig
```

##### Audio Driver
```bash
pacman -S pipewire pipewire-pulse wireplumber alsa-utils
```

##### Swapfile
```bash
fallocate -l 2G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
nano /etc/fstab
> /swapfile none swap defaults 0 0
```

##### ZRAM
```bash
pacman -S zram-generator
nano /etc/systemd/zram-generator.conf
> [zram0]
> zram-size = ram / 2
> compression-algorithm = zstd
> swap-priority = 100
> fs-type = swap
```

##### Encryption (optional)
```bash
nano /etc/mkinitcpio.conf
> HOOKS=(... block encrypt lvm2 filesystems ...)
mkinitcpio -P linux
```

##### Init
```bash
systemctl enable sshd
systemctl enable pipewire.socket
systemctl enable pipewire-pulse.socket
systemctl enable wireplumber.service
systemctl enable nvidia-persistenced
systemctl daemon-reexec
mkinitcpio -P
```

## 3.7 Root password
```bash
passwd
useradd -m -g users -G wheel UserName
passwd UserName

EDITOR=nano visudo
> %wheel ALL=(ALL:ALL) ALL
```

## 3.8 Boot loader
##### GRUB (MBR)
```bash
pacman -S grub
grub-install --target=i386-pc --boot-directory=/mnt/boot /dev/sdX --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

##### GRUB (GPT)
```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB --recheck
grub-mkconfig -o /boot/grub/grub.cfg
```

##### Encryption (optional)
```bash
pacman -S grub efibootmgr

nano /etc/default/grub
> GRUB_CMDLINE_LINUX_DEFAULT="loglevel=3 cryptdevice=/dev/sdXp3:volgroup0 quiet"

mount --mkdir /dev/sdXp1 /boot/EFI

grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
grub-mkconfig -o /boot/grub/grub.cfg
```

##### Syslinux (MBR)
```bash
pacman -S syslinux
mkdir -p /boot/syslinux
cp -r /usr/lib/syslinux/bios/*.c32 /boot/syslinux
extlinux --install /boot/syslinux
dd bs=440 count=1 conv=notrunc if=/usr/lib/syslinux/bios/mbr.bin of=/dev/sda
nano /boot/syslinux/syslinux.cfg
> UI menu.c32
> PROMPT 0
> TIMEOUT 30
> DEFAULT arch

> LABEL arch
>     MENU LABEL Arch Linux
>     LINUX /vmlinuz-linux
>     INITRD /initramfs-linux.img
>     APPEND root=UUID=XXXX-XXXX rw
```

##### systemd-boot (GPT)
```bash
bootctl install

nano /boot/loader/loader.conf
> default arch
> timeout 3
> editor no

mkdir -p /boot/loader/entries

nano /boot/loader/entries/arch.conf
> title   Arch Linux
> linux   /vmlinuz-linux
> initrd  /initramfs-linux.img
> options root=PARTUUID=YYYY-YYYY rw

!Note: To see UUID and PARTUUID use "blkid /dev/sdX2"
```

## 3.9 GUI
```bash
pacman -S xorg xorg-xrandr
```

##### GNOME
```bash
pacman -S gnome 
pacman -S gdm
systemctl enable gdm
```

##### Xfce
```bash
pacman -S xfce4 
pacman -S lightdm lightdm-gtk-greeter
systemctl enable lightdm
```

##### LXQt
```bash
pacman -S lxqt lxqt-session breeze-icons
pacman -S sddm
systemctl enable sddm
```

```bash
sudo pacman -S pcmanfm-qt       # Handles desktop wallpaper & icons
sudo pacman -S gvfs             # File system integration (removable drives)
sudo pacman -S picom            # Lightweight compositor (for smooth rendering)
sudo pacman -S xdg-user-dirs    # Auto-creates standard folders like Desktop

sudo pacman -S thunar-volman    # Automatic mounting of removable media
sudo pacman -S lxqt-qtplugin    # Better integration for Qt apps

pcmanfm-qt --desktop &
picom --vsync --config /dev/null &
```

##### !Remove
```bash
sudo pacman -Rns lxqt lxqt-session breeze-icons
```

```bash
sudo systemctl disable sddm
sudo pacman -Rns sddm
pacman -Qs sddm
```

# 4 Reboot
```bash
exit
umount -a
reboot
```

# 5 Post-installation
```bash
swapon --show
zramctl
free -h

speaker-test -t wav -c 2 -l 1
```

```bash
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

```bash
yay -Syu
yay -S neofetch
yay -S google-chrome
```

```bash
pacman -S fastfetch
pacman -S chromium
```