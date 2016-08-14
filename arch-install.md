# Installing Arch Linux

This documentation will go through the necessary steps to install a LVM on LUKS encrypted system

Note: This is only my experience, I highly suggest reading through the wiki and understanding everyting in detail


## Websites to have open (and follow through the tutorial):
* [https://wiki.archlinux.org/index.php/beginners'_guide](https://wiki.archlinux.org/index.php/beginners'_guide)
* [https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS)
* [https://wiki.archlinux.org/index.php/GRUB](https://wiki.archlinux.org/index.php/GRUB)


## Installation Guide :D

### Misc, good things to know Throughout!
* ``lsblk`` - will show all your drives and partitions
* ``blkid`` - good for getting the UUID for dvices
* ``networkmanager`` - install through pacman for good networking
    - Install this after :)

### 1. Setting everything up
1. Get your internet up and running
    - ``ping google.com.au``
    - If it doesn't work:
        - ``ip link`` - shows the wired connections
        - ``systemctl start dhcpcd@XXXX.service`` : where the XXX is your ethernet from ip link :)
			- e.g. ``systemctl start dhcpcd@eno0.service``
        - Now try ping

2. Set your time date with ntp : ``timedatectl set-ntp true``

### 2. Getting the drives ready

1. ``lsblk`` note your disk
2. Partition your disk: ``cfdisk /dev/sdX`` (x is the number of your drive)
    - Bootable drive: (for ``/boot``): 500M, type=LINUX, bootable.
        - I will refer to this partition as ``sdX1``
    - Rest of the partition: type=LINUX LVM
        - I will refer to this partition as ``sdX2``
3. ``cryptsetup luksFormat /dev/sdX1`` : the main partition (not boot)
    * Note: you can choose which type of Luks encryption to use here, please check [https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_an_entire_system#LVM_on_LUKS) documentation
4. ``cryptsetup open --type luks /dev/sdX2 lvm``
    - (note ``sdX2`` from above)
5. ``pvcreate /dev/mapper/lvm`` : create the physical volume
6. ``vgcreate volNameHere /dev/mapper/lvm`` : set your volume group name
7. ``lvcreate -L 4G volNameHere -n swap`` : set 4G swap
8. ``lvcreate -l 100%FREE volNameHere -n root``: set the rest of the drive to root.
    * Note the little `l`, it is used for percentages!
9. Format root: ``mkfs.ext4 /dev/mapper/volNameHere-root``
    1. Mount it: ``mount /dev/mapper/volNameHere-root /mnt``
10. Make Swap: ``mkswap /dev/mapper/volNameHere-swap``
    1. Mount it: ``swapon /dev/mapper/volNameHere-swap``
11. Get the boot device ready:
    1. ``mkfs.ext2 /dev/sdX1`` : format the boot partition
    2. ``mkdir /mnt/boot``
    3. ``mount /dev/sdX1 /mnt/boot`` : mount it

### 3. Time to install
1. Change your mirror list: ``vi /etc/pacman.d/mirrorlist``
    - Note: Australia should be moved to the top of the list
    - Note 2: Found (Aug 2016) that **Uber** was the fastest service
2. ``pacstrap -i /mnt base base-devel`` : install linux
    - **NOTE**: If you're getting an error here, make sure that you have mounted your root, home and swap properly (Steps 2.9.1, 2.10.1)
3. ``genfstab -U /mnt >> /mnt/etc/fstab`` : generate the fstab
4. ``arch-chroot /mnt /bin/bash`` : get into the arch shell on your drive
5. Run: ``locale-gen``
    - Create: ``/etc/locale.conf`` with your locale
        - inside file: ``LANG=en_US.UTF-8`` *(Note: follow the beginner guide about this)*
6. Selecting Time: *Follow the beginner guide for this too, explains it easier*
    1. ``tzselect`` (follow prompts)
    2. ``ln -s /usr/share/zoneinfo/Zone/SubZone /etc/localtime`` (make a symlink)
    3. ``hwclock --systohc --utc``
7. *(Follow the encrypting entire system LVM on LUKS)*:
    1. edit: ``/etc/mkinitcpio.conf``
        - Change: ``HOOKS="____ encrypt lvm2 filesystems ___"`` (make sure that encrypt and lvm2 and filesystems are inside that line)
            - Example of a complete line: ``HOOKS="base udev encrypt lvm2 autodetect modconf block filesystems keyboard fsck"``
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
    - Then: ``systemctl enable NetworkManager`` (so it starts on boot)

### 4. Finishing off the installation:
1. ``passwd`` (change your root password)
2. ``exit`` from your arch-chroot
3. ``umount -R /mnt`` (unmount the drive)
4. ``reboot``

AND YOU'RE SET!

## Now what?

* https://wiki.archlinux.org/index.php/general_recommendations#Users_and_groups
* https://wiki.archlinux.org/index.php/Users_and_groups#User_management

### Making a User
1. Add the group: ``groupadd userNameHere``
2. Create the user: ``useradd -m -g userNameHere -G wheel -s /bin/bash userNameHere``
    - ``-m`` creates the home directory
    - ``-g`` gives them a group to go in
    - ``-G`` is for additional groups, wheel is a core group in arch linux :)
3. Create the user password:
    - ``passwd userNameHere``

### Install your window managers etc.
Side note: make sure that you have a user with a password already created :)

1. ``pacman -S ____________`` < choose your window manager
    - Note: there are other steps depending on your window manager!
    - Note 2: Not sure which window manager to choose? Here's a [handy list](http://askubuntu.com/questions/65083/what-kinds-of-desktop-environments-and-shells-are-available)
2. Install your desktop manager (used for logging in etc.)
    * e.g. **Lightdm**
        1. ``pacman -S lightdm lightdm-gtk-greeter accountsservice``
        2. Get xorg : ``pacman -S xorg-server ...``
        3.  ***TODO*** : find which file to edit
3. Edit your ``~/.xinitrc`` to have the line ``exec windowManagerHere``

#### [GNOME] - easy install
To install gnome:

1. Install: ``pacman -S gnome gnome-extra``
2. Set the gdm: ``systemctl enable gdm``
3. Edit ``~/.xinitrc`` to have the line ``exec gnome-session``
4. Reboot and you're done: ``sudo reboot``

NOTE: For unknown reason, the default gnome terminal does not work. To get around this, use Teletype ``ctrl + alt + F3`` and download an alternative terminal e.g. ``pacman -S terminator``. ``ctrl + alt F1`` should bring you back to a logon screen.

#### [AwesomeWM]
1. Install: ``pacman -S awesome xorg xorg-server``
2. ** TODO: ** : *find files to edit to get awesome to work!*
2. (You will need a display manager to manage logging in / everything )

#### [i3]
1. Install: ``pacman -S i3 xorg xorg-server``
2. ** TODO ** : *find files to edit and get i3 working!*

# Problems and Fixes
1. LightDM is failing
    - If you go to a new tty ``alt+f2`` [or laptops: ``alt+fn+f2``] (note: some need control)
    - Look at ``systemctl status lightdm``
    - Did you install XORG?
2. Keyboard won't work to decrypt device (yes I ran into it)
    1. Solution 1 (all round):
        - Get into live CD
        - Go back to the step where you edit ``mkinitcpio.conf``
        - Remove the ``autodetect`` from the ``HOOKS``
        - ``mkinitcpio -p linux``
        - You *may* have to go through some of the install again
    2. Solution 2 (only keyboard)
        - Get into Live CD
        - Go back to step where you edit ``mkinitcpio.conf``
        - ON the ``HOOKS`` line move ``keyboards`` **before** autodetect.
        - ``mkinitcpio -p linux``
3. Wireless doesn't work?
    - Installing ``broadcom`` drivers (look at the arch wiki) [especially mac users, these are your drivers!]
    - Installing ``networkmanager`` is good for fixing some internet issues
