Build Bootable FreeBSD Image for Raspberry Pi 2 with Vagrant, Crochet, and Ansible
==================================================================================

This repo accompanies the blog post
`Build Bootable FreeBSD Image for Raspberry Pi 2 with Vagrant, Crochet, and Salt <http://www.codeghar.com/blog/build-bootable-freebsd-image-for-raspberry-pi-2-with-vagrant-crochet-and-salt.html>`_,
except here Ansible is used instead of SaltStack. While the blog details manual
steps this repo is more a clone-and-play type of deal.

How?
----

Install `Vagrant <https://www.vagrantup.com/>`_ (tested with v1.9.1),
`VirtualBox <https://www.virtualbox.org/>`_ (tested with v5.0.30), and
`Ansible <https://pypi.python.org/pypi/ansible>`_ (tested with v2.2.1.0 with
Python 3.5).

Clone this repo. Optionally, edit *Vagrantfile* or *playbook.yml* to fit your
requirements. Run Vagrant.

::

    user@host$ vagrant up

Once the machine is up and provisioned SSH into it.

::

    user@host$ vagrant ssh freebsd12

``cd`` to *~/crochet* and create an image. On my machine it took about
140 minutes to complete a build.

::

    vagrant@freebsd12$ cd /home/vagrant/crochet
    vagrant@freebsd12$ sudo /bin/sh crochet.sh -c config.rpi2.sh

Log out of the SSH session and copy the image to your machine.

::

    vagrant@freebsd12$ exit
    user@host$ vagrant scp freebsd12:/home/vagrant/crochet/work/FreeBSD-RPI2.img.xz .

Uncompress the image and write it to a MicroSD card.

::

    user@host$ xz -d FreeBSD-RPI2.img.xz
    user@host$ sudo dd if=FreeBSD-RPI2.img of=/dev/rdisk596870 bs=1m && sync

Image Customization
-------------------

I create an image with customizations -- such as network settings -- for my
hardware setup. Later I use Ansible to manage the box when it's up and
running.

These customizations go in the ``customize_freebsd_partition ( )`` section
in the config file. File with my customizations is in this repo and named
*config.rpi2.canakit.sh*.

**NOTE:** Modify *ssid* and *psk* (lines 318 and 319) values in
*config.rpi2.canakit.sh* before building the image or WLAN will not connect
after boot.

Updates
-------

In future -- when you need to build newer versions of FreeBSD -- update the
subversion repo and build a new image.

::

    user@host$ vagrant ssh freebsd12
    vagrant@freebsd12$ cd /home/vagrant/crochet/src
    vagrant@freebsd12$ svnlite update
    vagrant@freebsd12$ cd /home/vagrant/crochet
    vagrant@freebsd12$ rm -rf /home/vagrant/crochet/work
    vagrant@freebsd12$ sudo /bin/sh crochet.sh -c config.rpi2.sh

To update the Vagrant VM/box itself you need to destroy it and create a new
one. You will lose all data when you destroy Vagrant box.

::

    user@host$ vagrant box update
    user@host$ vagrant destroy -f
    user@host$ vagrant up
