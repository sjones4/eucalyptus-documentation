+++
title = "Browse and Download from Eucalyptus"
weight = 10
+++

Eucalyptus provides a few prebuilt "starter" images that you can download and install. This is one of the simplest ways to get started with Eucalyptus. Many other organizations, including Linux distributions, publish starter images for use on cloud systems, which can also be installed on Eucalyptus using the steps in this guide.With your Web browser, explore the Eucalyptus Machine Image catalog . Download an image of your choice (we suggest starting with images that have "hvm" as their Virtualization Type, as they are easier to install and work for both Instance Store and EBS-backed instances). Optionally, if the image is compressed (has a .tgz or .gz extension), you will have to uncompress it. If you obtained an HVM image, you now have a raw disk file that can be installed using instructions in the [Install an HVM image](img_task_install_hvm_image.dita#img_task_install_hvm_image) section. If you obtained a paravirtual image with kernel and ramdisk, you can install them with instructions in the [Install a paravirtual image](img_add_existing.dita#add_existing) section. 