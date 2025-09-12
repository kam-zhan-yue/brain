### Create a Bootable USX Linux Media File
- Downloaded `usbimager` as well as the `.iso` file of Arch Linux
- Wrote the `.iso` file to an empty flash drive with [this tutorial](https://www.youtube.com/watch?v=0xuP1GQLPpI)

An ISO Image or .iso file is a container format that holds the file system used on optical disks (CDs or DVDs) to store programs, movies, and other multimedia content. ISO is a formatting standard set by the International Standards Organisation.

It can be very convenient to store an operating system (OS) CD on a computer as an ISO image. 

## Boot using the ISO File
- Needed to turn off Secure Boot
- On Windows 11, hold SHIFT while clicking Restart
- Navigate to Troubleshoot > Advanced Options > UEFI Firmware Settings > Restart
- Turn off Secure Boot then Restart and boot with the USB

## Connecting to Wifi
```bash
ip addr show #should fail
iwctl #enter the wifi command prompt
station wlan0 get-networks #find your network
exit

iwctl --passphrase ${SECRET_PASSRHASE} station wlan0 connect ${CONNECTION}
```

### Connecting to SSH
If you want to SSH into the Linux device, you need to setup first.

```bash
#check if SSH is started already
systemctl status sshd
#if not, then start it
systemctl start sshd
#set a root password as SSH doesn't allow connections to root without a password
passwd
#find the username of the machine with
whoami
```

From another computer, you can SSH into the machine simply with
```bash
ssh username@ip_address

#example
~ via 🐍 v3.12.7
❯ ssh root@192.168.40.26
The authenticity of host '192.168.40.26 (192.168.40.26)' can't be established.
ED25519 key fingerprint is SHA256:MhRUIUJ//6eCpZFc5eI/WCyQ2jqIDKlBpCs76leb6tM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.40.26' (ED25519) to the list of known hosts.
root@192.168.40.26's password:
Permission denied, please try again.
root@192.168.40.26's password:
To install Arch Linux follow the installation guide:
https://wiki.archlinux.org/title/Installation_guide

For Wi-Fi, authenticate to the wireless network using the iwctl utility.
For mobile broadband (WWAN) modems, connect with the mmcli utility.
Ethernet, WLAN and WWAN interfaces using DHCP should work automatically.

After connecting to the internet, the installation guide can be accessed
via the convenience script Installation_guide.


root@archiso ~ #
```

## Partitioning
```bash
lsbkl #shows the current storage devices
fdisk /dev/nvme0n1 #target the harddrive with /dev/
```

This will bring you to the `fdisk` utility. We will create one partition for the boot
```bash
p #shows the patition layout of that section
g #gives us an empty partition table
n #creates a new partition
Partition Number: 1
First Sector: default
Last Sector: +1G #creates 1GB
```

Now, we will create another partition for EFI
```
n
Partition Number: default
First Sector: default
Last Sector: +1G
```

Now we need another partition for LVM. We want this to take up the remainder of our drive
```
n
Partition Number: default
First Sector: default
Last Sector: default
```

Now we want to set the types of the partition
```bash
t #set the ype
Partition Number: 3
Partition Type: 44
```

```bash
w #write
```

## Formatting Partitions

```bash
mkfs.fat -F32 /dev/nvme0n1p1
mkfs.ext4 /dev/nvme0n1p2
cryptsetup luksFormat /dev/nvme0n1p3
```

## Configure LVM

```bash
# open the partition
cryptsetup open --type luks /dev/nvme0n1p3 lvm
# create a physical partition for lvm
pvcreate /dev/mapper/lvm
# create a volume group
vgcreate volgroup0 /dev/mapper/lvm
# create a logical volume for the root
lvcreate -L 30GB volgroup0 -n lv_root
# and another one for the rest of your files
lvcreate -L 250GB volgroup0 -n lv_home

# print your volume groups
vgdisplay
# print your logical volumes
lvdisplay

# magic stuff
modprobe dm_mod
vgscan
vgchange -ay

# format the logic volumes we are using
mkfs.ext4 /dev/volgroup0/lv_root
mkfs.ext4 /dev/volgroup0/lv_home

# mount the root volume
mount /dev/volgroup0/lv_root /mnt
# make a new directory within mnt to mount the boot partition
mkdir /mnt/boot
mount /dev/nvme0n1p2 /mnt/boot

# mount the home directory
mkdir /mnt/home
mount /dev/volgroup0/lv_home /mnt/home
```

## Install Packages
```
pacstrap -i /mnt base
```

## Generate fstab file
This is required for mounting partitions at boot time
```bash
genfstab -U -p /mnt >> /mnt/etc/fstab
# status check
cat /mnt/etc/fstab
```

## arch-chroot
Open a command prompt in our in-progress installation to complete the process.

```bash
arch-chroot /mnt
# set the root password for the root installation
passwd
# create a user for ourselves
useradd -m -g users -G wheel ${USERNAME}
# create a password for the user
passwd ${USERNAME}

# install packages
pacman -S base-devel dosfstools grub efibootmgr gnome gnome-tweaks lvm2 mtools vim nvim networkmanager openssh os-prober sudo

# enable open ssh
systemctl enable sshd

# install linux
pacman -S linux linux-headers linux-lts linux-lts-headers
# might need this for proprietary hardware
pacman -S linux-firmware
```

## Install a GPU Driver
```bash
# list all your pci devices to find your GPU
lspci

# if you have an NVIDA GPU, you need specific packages
pacman -S nvidia nvidia-utils nvidia-lts
```

## Configure Linux and Settings
```bash
vim /etc/mkinitcpio.conf
# add encrypt and lvm2 in HOOKS 

mkinitcpio -p linux
mkinitcpio -p linux-lts

vim /etc/locale.gen
# uncomment the language you want
locale-gen

vim /etc/default/grub
# change GRUB_CMDLINE_LINUX)DEFAULT="loglevel=3 cryptdevice=/dev/nvme0n1p3:volgroup0 quiet"

# setup the EFI partition
mkdir /boot/EFI
mount /dev/nvme0n1p1 /book/EFI
grub-install --target=x86_64-efi --bootloader-id=grub_uefi --recheck

# copy a file into the boot directory
cp /usr/share/locale/en\@quot/LC_MESSAGES/grub.mo /boot/grub/locale/en.mo
grub-mkconfig -o /boot/grub/grub.cfg

# enable gdm, which gives a login screen when we first boot the system
systemctl enable gdm
systemctl enable NetworkManager

# exit the chroot environment
exit 
unmount -a
reboot
```
