---
title: "Fixing VM Guest Auto-Resizing on HiDPI Hosts"
date: 2024-03-25T21:44:36-04:00
draft: false
---

This is a short post that I hope helps someone else that ran into a similar problem I did.

I created a new Windows 10 virtual machine on a Linux host, using QEMU/KVM as the backend and Libvirt/virt-manager as the frontend. As I've done in the past, I used VirtIO devices, including a virtual QXL display adapter, and installed the SPICE guest tools after installation, in one part to allow for auto-resizing the guest resolution with the host window size.

However upon install, I found that the auto-resize feature was not working. After some trial and error, I found that auto-resizing worked when the host window was below a certain size - about half the size of my display. Upon trying to resize the window larger than that, it just stopped working altogether.

After some head scratching and comparing it to my desktop machine where a near-identical configuration worked just fine, I realized that the main difference was that this machine had a HiDPI display. It seems that the problem is that default video memory allocated to the VM (16MB) isn't enough to support guest resolutions above a certain size.

The fix was simple. In the virt-manager details page for the VM, I selected the `Video QXL` option, and switched to the XML view. I then changed the `vgamem` field from the default of `16384` to `65536` (64MB). A quick reboot of the VM and now guest autoresizing works perfectly.
