---
title: "VirtualBox: Network setup note"
categories: VirtualBox
permalink: virtualbox-network-setup.html
tags: [VirtualBox]
Summary: Setup network for a newly installed virtual machine (VM), allowing the VM to access Internet and 2-way communication between the Host and VM.
---
## Environment

**Host**: Windows 10
**VM**: Oracle Linux 8
**VirtualBox**: 6.1

## Task goals

- The VM can access Internet
- 2-way communication between Host and VM

## VirtualBox network modes overview

Before network setup, it's helpful to know about the basic concepts for each network mode and the differences between them in VirtualBox. Please see the official document [here](https://www.virtualbox.org/manual/UserManual.html#networkingdetails)

{% include image.html file="blog_imgs/VB_network_modes_overview.png" caption="Overview of network modes" %}

## Network setup

According to the above table, we can see that it's quite flexible to configure networks in VirtualBox, and there are multiple ways to achieve my goals. I have tried out two solutions - both work as expected, they are:

- Bridged mode
- NAP + Host-only

### Bridged mode

**Configuration:**

1. On VirtualBox, select the VM -> Settings -> Network -> Adapter 1

    {% include image.html file="blog_imgs/VB_network_modes_bridged.png" caption="Bridged mode config" %}

2. Start the VM and check IP

    {% include image.html file="blog_imgs/VB_network_modes_bridged2.png" caption="Check IP in bridged mode" %}
    The VM gets an IP and can access the Internet.

3. Check access between VM and Host

    {% include image.html file="blog_imgs/VB_network_modes_bridged3.png" caption="Check access in bridged mode" %}

    The VM cannot ping through the Host, and vice versa. I thought maybe something went wrong and got stuck, then I jumped to other tasks. Hours later, when I got back to the failure point and ran the same command, you know what, the Host and VM just magically can communicate with each other (see picture below). But I did nothing about the issue, why?

    Bewilderedly, I searched on the Internet and found that someone hit the same issue. It seems that there is latency after network configuration, so please retry step3 a few minutes later.

**Drawbacks of bridged mode:**

- Guests can’t be split into separate networks (not isolated)
- Sometimes doesn’t work; dependent on external DHCP server and ability to filter packets on a host network interface. Company networks might block your interface
- No easy option for a fixed IP since host network is a variable
- Not secure. The guest is exposed on the host's network

### NAP + Host-only

NAP mode allows VM to access Internet, while Host-only mode provides 2-way communication between Host and VM.

**Configuration:**

1. On VirtualBox, select the VM -> Settings -> Network -> Adapter 1

    {% include image.html file="blog_imgs/VB_network_modes_nat.png" caption="NAT mode" %}

2. On VirtualBox, select the VM -> Settings -> Network -> Adapter 2

    {% include image.html file="blog_imgs/VB_network_modes_hostonly.png" caption="Host-only mode" %}

3. Start the VM, then turn on “Ethernet (enp0s8) Connection Adapter”

    After turning on “Ethernet (enp0s8) Connection Adapter”, an IP is assigned to enp0s8.

    {% include image.html file="blog_imgs/VB_network_turn_on_ethernet.png" caption="Turn on enp0s8 adapter" %}

    {% include note.html content="When ping Host from VM, should use the fixed IP: 10.0.2.2" %}

Due to the drawbacks listed for Bridged mode, I finally chose the NAP + Host-only solution for my VM network setting.

{% include links.html %}