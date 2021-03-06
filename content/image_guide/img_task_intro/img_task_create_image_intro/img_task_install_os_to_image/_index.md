+++
title = "Create an HVM Image from a Linux Distribution ISO (KVM)"
weight = 10
+++

This topic shows how to install a Linux distribution ISO and prepare an image for registration with Eucalyptus.Use the QEMU disk utility to create a disk image. In the following example, we create a 5GB disk image: `qemu-img create -f raw centos7.img 5G` Use the `parted` utility to set the disk label. `parted centos7.img mklabel msdos` Use `virt-install` to start a new virtual machine installation, as in the following example (note the example has been broken into multiple lines for formatting purposes): 

    virt-install --name centos7 --ram 1024 --os-type linux --os-variant rhel7 
     -c /tmp/CentOS-7.0-x86_64-bin-DVD1.iso --disk path=/tmp/centos7.img,device=disk,bus=virtio 
     --graphics vnc,listen=0.0.0.0 --force 

Use the VNC client of your choice to connect to the new virtual machine and complete the installation. Modify the following `libvirt.xml` template to create the VM and run `virsh create <libvirt.xml>"` . 

     <domain type='kvm'>
        <name>eucalyptus-centos</name>
        <os>
            <type>hvm</type>
        </os>
        <features>
            <acpi/>
        </features>
        <memory>1073741</memory>
        <vcpu>1</vcpu>
        <devices>
            <!--<emulator>/usr/bin/kvm</emulator>-->
            <disk type='file'>
                <source file='/tmp/centos7.img'/>
                <target dev='hda'/>
            </disk>
         <interface type='bridge'>
             <source bridge='br0'/>
             <model type='virtio'/>
         </interface>
            <graphics type='vnc' port='-1' autoport='yes' listen='0.0.0.0'/>
        </devices>
    </domain>

Connect to the virtual machine using your VNC client of choice and make the following configuration changes: Modify the `/etc/sysconfig/network-scripts/ifcfg-eth0` file and set the `ONBOOT` option to "yes". For example: 

    DEVICE="eth0"
    BOOTPROTO="dhcp"
    #HWADDR="B8:AC:6F:83:1C:45"
    IPV6INIT="yes"
    MTU="1500"
    NM_CONTROLLED="yes"
    ONBOOT="yes"
    TYPE="Ethernet"
    UUID="499c07cc-4a53-408c-87d2-ce0db991648e"
    PERSISTENT_DHCLIENT=1

Enable a serial console on the virtual machine by adding the following option to the end of the /boot/grub/menu.lst: `console=ttyS0` Remove the `quiet` option from the kernel parameters and the grub menu splash image in the `/boot/grub/menu.lst` file. For example: 

    # grub.conf generated by anaconda
    #
    # Note that you do not have to rerun grub after making changes to this file
    # NOTICE:  You have a /boot partition.  This means that
    #          all kernel and initrd paths are relative to /boot/, eg.
    #          root (hd0,0)
    #          kernel /vmlinuz-version ro root=/dev/sda2
    #          initrd /initrd-[generic-]version.img
    #boot=/dev/sda
    default=0
    timeout=5
    #splashimage=(hd0,0)/grub/splash.xpm.gz
    hiddenmenu
    title Eucalyptus (n.n.32-358.18.1.el7.x86_64)
    	root (hd0,0)
    	kernel /vmlinuz-n.n.32-358.18.1.el7.x86_64 ro root=UUID=062b9c31-95f3-424f-8b47-35107cfdfc08 rd_NO_LUKS rd_NO_LVM LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb
    	initrd /initramfs-n.n.32-358.18.1.el7.x86_64.img

Add the following line to the `/etc/sysconfig/network` file to disable the zeroconf route, which can interfere with access to the metadata service: `NOZEROCONF=yes` Edit the `/etc/udev/rules.d/70-persistent-net.rules` file and remove all entries for the existing network interface. Make sure that you delete all entries before terminating the virtual machine and registering it with Eucalyptus. Eucalyptus instances use cloud-init to specify actions to run on your instance at boot time, which can be passed using the userdata parameter. To install and configure `cloud-init` on your instance: Install cloud-init: 

    # rpm -Uvh http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    # yum install cloud-init

By default, cloud-init uses ec2-user as the log-in user. Add ec2-user to your instance and give it appropriate sudo permissions: 

     # adduser ec2-user
     # passwd ec2-user

Run visudo and add the following entries at the bottom of the sudoers file: 

    ## Allow root to run any commands anywhere
         root    ALL=(ALL)       ALL 
         ec2-user        ALL=(ALL)       NOPASSWD: ALL  

You can optionally copy Eucalyptus `rc.local` file (available at [](https://github.com/eucalyptus/Eucalyptus-Scripts/blob/master/rc.local) ) to the `/etc` directory on your virtual machine and modify it as needed. You now have a raw disk file that can be installed using instructions in the [Install an HVM image]({{< ref img_task_install_hvm_image.md >}}) section. 
