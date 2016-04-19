Build Bootable FreeBSD Image for Raspberry Pi 2 with Vagrant, Crochet, and Ansible
==================================================================================

This repo accompanies the blog post
`Build Bootable FreeBSD Image for Raspberry Pi 2 with Vagrant, Crochet, and Salt <http://www.codeghar.com/blog/build-bootable-freebsd-image-for-raspberry-pi-2-with-vagrant-crochet-and-salt.html>`_,
except here Ansible is used instead of SaltStack. While the blog details manual
steps this repo is more a clone-and-play type of deal.

How?
----

Install `Vagrant <https://www.vagrantup.com/>`_,
`VirtualBox <https://www.virtualbox.org/>`_, and
`Ansible <https://pypi.python.org/pypi/ansible>`_.

Clone this repo. Optionally, edit *Vagrantfile* or *playbook.yaml* to fit your
requirements. Run Vagrant.

::

    user@host$ vagrant up

Once the machine is up and provisioned SSH into it.

::

    user@host$ vagrant ssh freebsd11

``cd`` to *~/crochet* and create an image. On my machine it took about
140 minutes to complete a build.

::

    vagrant@freebsd11$ cd /home/vagrant/crochet
    vagrant@freebsd11$ sudo /bin/sh crochet.sh -c config.rpi2.sh

Log out of the SSH session and copy the image to your machine.

::

    vagrant@freebsd11$ exit
    user@host$ vagrant scp freebsd11:/home/vagrant/crochet/work/FreeBSD-RPI2.img.xz .

Uncompress the image and write it to an SD card.

::

    user@host$ xz -d FreeBSD-RPI2.img.xz
    user@host$ sudo dd if=FreeBSD-RPI2.img of=/dev/rdisk596870 bs=1m && sync

In future -- when you need to build newer versions -- update the subversion
repo and build a new image.

::

    user@host$ vagrant ssh freebsd11
    vagrant@freebsd11$ cd /home/vagrant/crochet/src
    vagrant@freebsd11$ svnlite update
    vagrant@freebsd11$ cd /home/vagrant/crochet
    vagrant@freebsd11$ rm -rf /home/vagrant/crochet/work
    vagrant@freebsd11$ sudo /bin/sh crochet.sh -c config.rpi2.sh
