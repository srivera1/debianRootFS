# debianRootFS
## Steps for the creation of a root file system for the pynq board

### sergio rivera 2018

based on the info from https://olimex.wordpress.com/2014/07/21/how-to-create-bare-minimum-debian-wheezy-rootfs-from-scratch/

#at host:

sudo apt-get install qemu-user-static debootstrap binfmt-support

targetdir=rootfs

distro=stable

mkdir $targetdir

sudo debootstrap --arch=armhf --foreign $distro $targetdir

sudo cp /usr/bin/qemu-arm-static $targetdir/usr/bin/

sudo cp /etc/resolv.conf $targetdir/etc/

sudo chroot $targetdir/



#in chrooted enviroment:

distro=stable

export LANG=C

/debootstrap/debootstrap --second-stage

cat <<EOT > /etc/apt/sources.list

deb http://debian.netcologne.de/debian/ $distro main non-free contrib

deb-src http://debian.netcologne.de/debian/ $distro main non-free contrib

deb http://debian.netcologne.de/debian/ $distro-updates main contrib non-free

deb-src http://debian.netcologne.de/debian/ $distro-updates main contrib non-free

deb http://security.debian.org/debian-security $distro/updates main contrib non-free

deb-src http://security.debian.org/debian-security $distro/updates main contrib non-free

EOT

apt-get update

apt-get install locales dialog

dpkg-reconfigure locales

apt-get install openssh-server ntpdate sudo

// set passwd to passwd

passwd

echo debianRootfs > /etc/hostname

cat <<EOT > /etc/network/interfaces

auto lo

iface lo inet loopback

EOT





adduser user001

usermod -aG sudo user001



apt-get clean

echo TO:2345:respawn:/sbin/getty -L ttyS0 115200 vt100 >> /etc/inittab

sudo rm $targetdir/etc/resolv.conf

sudo rm $targetdir/usr/bin/qemu-arm-static



exit



# coping file system keeping file permissions

cd rootfs/

tar cvpzf rootfs.tar.gz

sudo -H tar --strip-components=1 -xzvphf rootfs.tar.gz -C <SD Mount Point Directory>




#boot your system, connect to the serial port (/dev/ttyUSBX) and you can login

(an ethernet ssh session would work too):



[21:47:10:366] Debian GNU/Linux 9 debianRootfs ttyPS0␍␊

[21:47:10:366] ␍␊

[21:47:10:398] debianRootfs login: user001␍␍␊

[21:47:14:814] Password: ␍␊

[21:47:18:094] Last login: Thu Dec 13 10:00:46 UTC 2018 on UNKNOWN␍␊

[21:47:18:126] Linux debianRootfs 4.9.0-xilinx-dirty #2 SMP PREEMPT Thu Nov 29 12:57:11 CET 2018 armv7l␍␊

[21:47:18:142] ␍␊

[21:47:18:142] The programs included with the Debian GNU/Linux system are free software;␍␊

[21:47:18:142] the exact distribution terms for each program are described in the␍␊

[21:47:18:142] individual files in /usr/share/doc/*/copyright.␍␊

[21:47:18:158] ␍␊

[21:47:18:158] Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent␍␊

[21:47:18:158] permitted by applicable law.␍␊

