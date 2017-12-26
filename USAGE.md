You need [Virtualbox](https://www.virtualbox.org) and [Vagrant](https://www.vagrantup.com/).

# Error is fine, it hasn't yet anything to boot. Just turn the test machine off
`vagrant up test`
`vagrant up template`
# Ansible is used for provisioning, so this step does most of provisioning for template machine
`vagrant up server`
# to boot with new kernel
`vagrant reload template`

`vagrant ssh template`

Here you can customize your future thin client: install packages, configs, ...

Do not:
* Remove package virtualbox-guest-utils
* Remove openssh-server
* Change type and settings of first interface

If you do one of this actions, then you should do further work inside template virtual machine manually, without vagrant.

After work, inside template machine:

`cd /vagrant`
`./build.sh all`

You will get this artifacts in vagrant/build directory:
* `vmlinuz` - linux kernel
* `initrd.img` - initial ram disk
* `rootfs.squashfs` - root FS image
* `home.tar.gz` - compressed home directory for user ubuntu
* `pxelinux.0`, `ldlinux.c32` - [pxelinux](http://www.syslinux.org/wiki/index.php?title=PXELINUX) network bootloader files
* `pxelinux.cfg/default` - configuration for pxelinux. Boot parameters are set here.

Notice: ssh keys for ubuntu user, generated by Vagrant, are not included in home directory for security reasons.

Open Virtualbox and remove SCSI controller from test machine. With my version of linux kernel and virtualbox it sometimes makes kernel crash. Some rare bug, may not affect you.

Server machine is cofigured as DHCP and TFTP server(dnsmasq), and HTTP server(nginx). HTTP is used to transfer huge(hundreds of megabytes) rootfs image and home directory image. In test environment you can transfer huge files with TFTP. But in real networks, where packets can be lost sometimes, it is not very suitable for this task.

Start test machine. If everything is OK, it should boot with your image over network from server machine.