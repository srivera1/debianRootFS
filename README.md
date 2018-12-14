# debianRootFS

// sergio rivera 2018
// steps for the creation of a root file system for the pynq board
// based on the info from olimex.wordpress.com/2014/07/21/how-to-create...

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





[11:41:11:978] ## Booting kernel from Legacy Image at 02080000 ...␍␊
[11:41:11:994]    Image Name:   Linux-4.9.0-xilinx+␍␊
[11:41:11:994]    Image Type:   ARM Linux Kernel Image (uncompressed)␍␊
[11:41:11:994]    Data Size:    4035904 Bytes = 3.8 MiB␍␊
[11:41:11:994]    Load Address: 00008000␍␊
[11:41:12:010]    Entry Point:  00008000␍␊
[11:41:12:010]    Verifying Checksum ... OK␍␊
[11:41:12:058] ERROR: Did not find a cmdline Flattened Device Tree␍␊
[11:41:12:058] Could not find a valid device tree␍␊
[11:41:12:058] Zynq> 








[11:11:33:273] ALSA device list:␍␊
[11:11:33:273]   No soundcards found.␍␊
[11:11:33:273] Waiting 3 sec before mounting root device...␍␊
[11:11:36:361] VFS: Cannot open root device "mmcblk0p2" or unknown-block(0,0): error -6␍␊
[11:11:36:377] Please append a correct "root=" boot option; here are the available partitions:␍␊
[11:11:36:377] 0100           16384 ram0  (driver?)␍␊
[11:11:36:377] 0101           16384 ram1  (driver?)␍␊
[11:11:36:393] 0102           16384 ram2  (driver?)␍␊
[11:11:36:393] 0103           16384 ram3  (driver?)␍␊
[11:11:36:393] 0104           16384 ram4  (driver?)␍␊
[11:11:36:393] 0105           16384 ram5  (driver?)␍␊
[11:11:36:393] 0106           16384 ram6  (driver?)␍␊
[11:11:36:409] 0107           16384 ram7  (driver?)␍␊
[11:11:36:409] 0108           16384 ram8  (driver?)␍␊
[11:11:36:409] 0109           16384 ram9  (driver?)␍␊
[11:11:36:409] 010a           16384 ram10  (driver?)␍␊
[11:11:36:409] 010b           16384 ram11  (driver?)␍␊
[11:11:36:425] 010c           16384 ram12  (driver?)␍␊
[11:11:36:425] 010d           16384 ram13  (driver?)␍␊
[11:11:36:425] 010e           16384 ram14  (driver?)␍␊
[11:11:36:425] 010f           16384 ram15  (driver?)␍␊
[11:11:36:441] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)␍␊
[11:11:36:441] CPU1: stopping␍␊
[11:11:36:441] CPU: 1 PID: 0 Comm: swapper/1 Not tainted 4.9.0-xilinx+ #3␍␊
[11:11:36:441] Hardware name: Xilinx Zynq Platform␍␊
[11:11:36:457] [<c010e48c>] (unwind_backtrace) from [<c010a664>] (show_stack+0x10/0x14)␍␊
[11:11:36:457] [<c010a664>] (show_stack) from [<c02ede4c>] (dump_stack+0x80/0xa0)␍␊
[11:11:36:457] [<c02ede4c>] (dump_stack) from [<c010cc0c>] (ipi_cpu_stop+0x3c/0x70)␍␊
[11:11:36:473] [<c010cc0c>] (ipi_cpu_stop) from [<c010d414>] (handle_IPI+0x64/0x84)␍␊
[11:11:36:473] [<c010d414>] (handle_IPI) from [<c01013f8>] (gic_handle_irq+0x78/0x94)␍␊
[11:11:36:489] [<c01013f8>] (gic_handle_irq) from [<c010b08c>] (__irq_svc+0x6c/0xa8)␍␊
[11:11:36:489] Exception stack(0xdf471f78 to 0xdf471fc0)␍␊
[11:11:36:489] 1f60:                                                       00000000 00000000␍␊
[11:11:36:505] 1f80: c0947b00 dfba7b00 00000001 dfba6e40 f7f6eb89 00000000 f7ba567c 00000000␍␊
[11:11:36:505] 1fa0: 00000000 00000000 0000000b df471fc8 c05062fc c0506320 60000013 ffffffff␍␊
[11:11:36:505] [<c010b08c>] (__irq_svc) from [<c0506320>] (cpuidle_enter_state+0xe4/0x1c0)␍␊
[11:11:36:521] [<c0506320>] (cpuidle_enter_state) from [<c014d0d0>] (cpu_startup_entry+0x158/0x1a4)␍␊
[11:11:36:521] [<c014d0d0>] (cpu_startup_entry) from [<001014ac>] (0x1014ac)␍␊
[11:11:36:537] ---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)


















