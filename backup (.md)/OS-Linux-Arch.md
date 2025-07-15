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

<div style="display:flex; gap:1rem; align-items:flex-start;">
  <div style="width:50%">
    <strong><i>parted (MBR)</i></strong>
    <pre>
parted /dev/sdX --align optimal
> mklabel msdos
> mkpart primary ext4 1MiB 513MiB
> set 1 boot on
> mkpart primary linux-swap 513MiB 2561MiB
> mkpart primary ext4 2561MiB 100%
> quit
&nbsp;
parted /dev/sdY --align optimal
> mklabel msdos
> mkpart primary ntfs 1MiB 100%
> quit
    </pre>
  </div>

  <div style="width:50%">
    <strong><i>parted (GPT)</i></strong>
    <pre>
parted /dev/sdX --align optimal
> mklabel gpt
> mkpart primary fat32 1MiB 513MiB
> set 1 boot on
> mkpart primary ext4 513MiB 100%
> quit
&nbsp;
parted /dev/sdY --align optimal
> mklabel gpt
> mkpart primary ntfs 1MiB 100%
> quit
    </pre>
  </div>
</div>


<div style="display:flex; gap:1rem; align-items:flex-start;">
  <div style="width:50%">
    <strong><i>fdisk (MBR) | gdisk (GPT)</i></strong>
    <pre>
fdisk /dev/sdX
> p (print partition table)
> o (mbr) | g (gpt)
&nbsp;
-----/mnt/boot-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector: +512M
> t (type)
> 83 (Linux)
&nbsp;
-----/mnt-----
> n (new partition)
> Partition number: 2
> First sector:
> Last sector:
> t (type)
> 83 (Linux)
&nbsp;
-----swap-----
> n (new partition)
> Partition number: 3
> First sector:
> Last sector: +2G
> t (type)
> 82 (Linux swap)
&nbsp;
> w
&nbsp;
&nbsp;
fdisk /dev/sdY
> p (print partition table)
> o (mbr) | g (gpt)
&nbsp;
-----/mnt/CommonDisk-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector:
> t (type)
> 07 (NTFS)
&nbsp;
> w
    </pre>
  </div>

  <div style="width:50%">
    <strong><i>Encryption (optional)</i></strong>
    <pre>
fdisk /dev/sdX
> p (print partition table)
> g (gpt)
&nbsp;
-----/boot/EFI-----
> n (new partition)
> Partition number: 1
> First sector:
> Last sector: +1G
&nbsp;
-----/mnt/boot-----
> n (new partition)
> Partition number: 2
> First sector:
> Last sector: +1G
&nbsp;
-----/mnt (volgroup0)-----
> n (new partition)
> Partition number: 3
> First sector:
> Last sector:
&nbsp;
> t (type)
> 44 (Linux LVM)
&nbsp;
> w
    </pre>
  </div>
</div>


## 1.10 Format the partitions
<div style="display:flex; gap:1rem; align-items:flex-start;">
  <div style="width:50%">
    <strong><i>format partitions</i></strong>
    <pre>
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2
mkswap /dev/sdX3
&nbsp;
mkfs.ntfs -f /dev/sdY1
    </pre>
  </div>

  <div style="width:50%">
    <strong><i>encryption (optional)</i></strong>
    <pre>
mkfs.fat -F32 /dev/sdX1
mkfs.ext4 /dev/sdX2
cryptsetup luksFormat /dev/sdX3
&nbsp;
cryptsetup open --type luks /dev/sdX3 lvm
pvcreate /dev/mapper/lvm
vgcreate volgroup0 /dev/mapper/lvm
lvcreate -L 30GB volgroup0 -n lv_root
lvcreate -L 100%FREE volgroup0 -n lv_home
&nbsp;
vgdisplay | lvdisplay
&nbsp;
modprobe dm_mod
vgscan
vgchange -ay
&nbsp;
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home
    </pre>
  </div>
</div>


## 1.11 Mount the file systems
<div style="display:flex; gap:1rem; align-items:flex-start;">
  <div style="width:50%">
    <strong><i>mount disks</i></strong>
    <pre>
mount /dev/sdX2 /mnt
mount --mkdir /dev/sdX1 /mnt/boot
swapon /dev/sdX3
&nbsp;
mount --mkdir /dev/sdY1 /mnt/CommonDisk
    </pre>
  </div>

  <div style="width:50%">
    <strong><i>encryption (optional)</i></strong>
    <pre>
mount /dev/volgroup0/lv_root /mnt
mount --mkdir /dev/volgroup0/lv_home /mnt/home
mount --mkdir /dev/sdX2 /mnt/boot
    </pre>
  </div>
</div>

# 2 Installation
## 2.1 Select the mirrors
```bash
reflector --country Hungary,Austria --age 12 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
less /etc/pacman.d/mirrorlist
```

## 2.2 Install essential packages
```bash
pacstrap -K /mnt base linux linux-firmware nano
```

# 3 Configure the system
## 3.1 Fstab
```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
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
> en_GB.UTF-8 UTF-8
> tr_TR.UTF-8 UTF-8
locale-gen

nano /etc/locale.conf
> LANG=en_GB.UTF-8
> LC_TIME=tr_TR.UTF-8
> LC_NUMERIC=tr_TR.UTF-8

nano /etc/vconsole.conf
> KEYMAP=tr_q
> FONT=lat2-16
> FONT_MAP=8859-9
```

## 3.5 Network configuration
```bash
echo HostName > /etc/hostname

cat /etc/hosts
> 127.0.0.1   localhost
> ::1         localhost
> 127.0.1.1   HostName.localdomain HostName

systemctl enable NetworkManager
```

## 3.6 Initramfs
##### Packages
```bash
pacman -S networkmanager linux-headers dosfstools mtools sudo lvm2 os-prober base-devel openssh
```

##### Graphic Driver
```bash
lspci | grep -E "VGA|3D"
pacman -S mesa           	    # OpenGL/Vulkan for Intel & AMD
pacman -S intel-media-driver  	# VA‑API for Intel GPUs
pacman -S libva-mesa-driver   	# VA‑API for AMD GPUs
pacman -S nvidia nvidia-utils 	# NVIDIA proprietary drivers
```

##### Audio Driver
```bash
pacman -S pipewire pipewire-alsa pipewire-pulse alsa-utils
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
pacman -S systemd-zram-generator
nano /etc/systemd/zram-generator.conf
> [zram0]
> zram-size = ram / 2
> compression-algorithm = zstd
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
systemctl enable pipewire.service
systemctl enable pipewire-pulse.service
systemctl enable alsa-restore.service
systemctl daemon-reexec
mkinitcpio -P
```

## 3.7 Root password
```bash
passwd
useradd -m -g users -G wheel <UserName>
passwd <UserName>
```

## 3.8 Boot loader
##### GRUB (MBR)
```bash
pacman -S grub
grub-install --target=i386-pc /dev/sda --recheck
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
nano /boot/syslinux/syslinux.cfg
dd bs=440 count=1 conv=notrunc if=/usr/lib/syslinux/bios/mbr.bin of=/dev/sda
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
> options root=PARTUUID=<uuid> rw
```

## 3.9 GUI
##### GNOME
```bash
pacman -S gnome gdm
systemctl enable gdm
```

##### Xfce
```bash
pacman -S xfce4 lightdm lightdm-gtk-greeter
systemctl enable lightdm
```

##### LXQt
```bash
pacman -S lxqt breeze-icons pcmanfm-qt qterminal lxqt-sudo sddm sddm-qt5-greeter
systemctl enable sddm
```

# 4 Reboot
```bash
exit
umount -a
reboot
```

# 5 Post-installation
```bash

```