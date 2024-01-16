---
title: "Libvirt in Podman"
date: 2024-01-16T07:45:17-05:00
draft: false
---

Running Silverblue on my primary machine, one of the things that I miss most in the base distribution is Libvirt and QEMU/KVM. There are a few simple options for this - one would be to simply layer the necessary packages with `rpm-ostree`. However, I try to layer as few packages as possible and keep my system as close to the base tree as possible. There's also a flatpak for GNOME Boxes that can get you quickly up and running, but it's fairly bare bones and makes it difficult to do any configuration beyond basic parameters without diving into XML editing.

However, what Silverblue does have is Podman. Podman and Toolbox are recommended for running containerized software that you either don't want to layer, or isn't available in Flatpak form, and Libvirt is no exception.

To start I create a new privileged container:

`$ sudo podman run -d --name virtbox --privileged --net=host -v /proc/modules:/proc/modules -v /var/lib/libvirt/:/var/lib/libvirt:z -v /sys/fs/cgroup:/sys/fs/cgroup:rw registry.fedoraproject.org/fedora-minimal:latest`

I make sure to pass `--net=host` so that we can SSH into the container later on and that our VMs can talk out. We pass through `/proc/modules` and `/sys/fs/cgroup` as well so that our container has access to full KVM virtualization. Finally, I pass through a directory from the host, in this case `/var/lib/libvirt` where we can store our configuration and our VM images.

Once the container is created, enter it with:

`$ sudo podman exec -it virbox /bin/bash`

Inside the container, we have access to microdnf, a lighter version of the DNF package manager. We'll use it to install the packages we need for virtualization.

`# microdnf install passwd libvirt qemu-kvm systemd nc openssh-server passt --nodocs`

In order to manage libvirt, we'll SSH into the container from the host. To do that, we'll set up sshd on the container to listen on an alternate port. Within the container's `/etc/ssh/sshd_config`, add the following lines:

`Port 2222
ListenAddress 127.0.0.1
PermitRootLogin yes`

Be sure to set a root password as well.


Finally, enable your services:

`# systemctl enable --now sshd libvirtd`

From your host, or another container on the system, run `virt-manager` and connect to your libvirt container with `root` and `localhost:2222`. You may also want to copy your SSH keys to the container so it doesn't constantly ask for your password with `$ ssh-copy-id -p 2222 root@localhost`
