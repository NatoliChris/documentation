# Installing Arch Linux

This documentation will go through the necessary steps to install a LVM on LUKS encrypted system

Note: This is only my experience, I highly suggest reading through the wiki and understanding everyting in detail


## Websites to have open (and follow through the tutorial):
* [https://wiki.archlinux.org/index.php/beginners'_guide](https://wiki.archlinux.org/index.php/beginners'_guide)
* [https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS)
* [https://wiki.archlinux.org/index.php/GRUB](https://wiki.archlinux.org/index.php/GRUB)


## Installation Guide :D

### Misc, good things to know!
* ``lsblk`` - will show all your drives and partitions
* ``blkid`` - good for getting the UUID for dvices
* ``networkmanager`` - install through pacman for good networking

### 1. Setting everything up
1. Get your internet up and running
    - ``ping google.com.au``
    - If it doesn't work:
        - ``ip link`` - shows the wired connections
        - ``systemctl start dhcpcd@XXXX.service`` : where the XXX is your ethernet from ip link :)
        - Now try ping

2. Set your time date with ntp : ``timedatectl set-ntp true``

### 2. Getting the drives ready

1. ``lsblk`` note your disk
2. Partition your disk: ``cfdisk /dev/sdX`` (x is the number of your drive)
    - Bootable drive: (for ``/boot``): 500M, type=LINUX, bootable.
        - I will refer to this partition as ``sdX1``
    - Rest of the partition: type=LINUX LVM
        - I will refer to this partition as ``sdX2``
3. ``cryptsetup luksFormat /dev/sdX_`` : the main partition (not boot)
    * Note: you can choose which type of Luks encryption to use here, please check [https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS) documentation
4. ``cryptsetup open --type luks /dev/sdX2 lvm``
    - (note ``sdX2`` from above)
5. ``pvcreate /dev/mapper/lvm`` : create the physical volume
6. ``vgcreate volNameHere /dev/mapper/lvm`` : set your volume group name
7. ``lvcreate -L 4G volNameHere -n swap`` : set 4G swap
8. ``lvcreate -l 100%FREE volNameHere -n root``: set the rest of the drive to root.
    * Note the little `l`, it is used for percentages!
9. Format root: ``mkfs.ext4 /dev/mapper/volNameHere-root``
10. Make Swap: ``mkswap /dev/mapper/volNameHere-swap``
11. Get the boot device ready:
    1. ``mkfs.ext2 /dev/sdX1`` : format the boot partition
    2. ``mkdir /mnt/boot``
    3. ``mount /dev/sdX1 /mnt/boot`` : mount it

### 3. Time to install
1. Change your mirror list: ``vi /etc/pacman.d/mirrorlist``
    - Note: Australia should be moved to the top of the list
    - Note 2: Found (Aug 2016) that **Uber** was the fastest service
2. ``pacstrap -i /mnt base base-devel`` : install linux
3. ``genfstab -U /mnt >> /mnt/etc/fstab`` : generate the fstab
4. ``arch-chroot /mnt /bin/bash`` : get into the arch shell on your drive
5. Run: ``locale-gen``
    - Create: ``/etc/locale.conf`` with your locale
        - inside file: ``LANG=en_US.UTF-8`` *(Note: follow the beginner guide about this)*
6. Selecting Time:
    1. ``tzselect`` (follow prompts)
    2. ``ln -s /usr/share/zoneinfo/Zone/SubZone /etc/localtime`` (make a symlink)
    3. ``hwclock --systohc --utc``
7. *(Follow the encrypting entire system LVM on LUKS)*:
    1. edit: ``/etc/mkinitcpio.conf``
        - Change: ``HOOKS="____ encrypt lvm2 filesystems ___"`` (make sure that encrypt and lvm2 and filesystems are inside that line)
            - **NOTE**: if you are on a laptop or desktop computer and want a portable USB version of your arch linux, then you can take out the *'autodetect'* from that hook, it will install all drivers for keyboards etc.
8. ``mkinitcpio -p linux``

### 4. Bootloader time (GRUB with MBR)
Note before: I recommend reading along the GRUB link as well as beginner guide and the encrypting system, it has fine details that I may have missed.

1. ``pacman -S grub``
2. ``grub-install --target=i386-pc /dev/sdX`` (e.g. /dev/sdc) [grub will detect the boot]
3. edit the grub defaults:
    1. ``blkid`` to find the UUID of your crypted device (find UUID of /dev/sdX2 [the encrypted partition])
    2. edit ``/etc/default/grub`` :
        * In the 'GRUB_CMDLINE_LINUX' variable put this string:
    ``cryptdevice=UUID='device-uuid-here':lvm root=/dev/mapper/volNameHere-root``
4. ``grub-mkconfig -o /boot/grub/grub.cfg`` 

### 5. Network and Hostname
1. edit ``/etc/hostname`` and put your hostname in the first line
2. edit ``/etc/hosts`` [optional] and replace localhost with your hostname
3. Installing good network managers: ``pacman -S networkmanager``

### 4. Finishing off the installation:
1. ``passwd`` (change your root password)
2. ``exit`` from your arch-chroot
3. ``umount -R /mnt`` (unmount the drive)
4. ``reboot``

AND YOU'RE SET!
