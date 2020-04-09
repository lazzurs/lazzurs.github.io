---
layout: post
title:  "VyOS upgrades"
date:   2020-04-09 00:00:00 +0000
categories: vyos upgrade
---
Today I hit some legacy infrastructure and when ever I hit something like this I can't help but scratch that itch.

We have been using instances with [VyOS][vyos] for routers all over our AWS infrastructure and it turns out they were
never updated after being installed. Given these are the routers for our network they needed to be upgraded.

We were on version 1.0.2 which was released on February 3, 2014. The release is so old the upgrade instructions were not
even on the current documentation site. To upgrade from 1.0.2 to the latest version 1.3 it requires two steps covered in
the [old VyOS documentation site][vyos-old-docs].

First off upgrade to 1.1.6 to handle a GPG key rollover

    add system image https://downloads.vyos.io/release/legacy/1.1.6/vyos-1.1.6-amd64.iso

This then asked the following questions, the default answers were perfect.

    What would you like to name this image? [1.1.6]:
    Would you like to save the current configuration directory and config file? (Yes/No) [Yes]:
    Would you like to save the SSH host keys from your current configuration? (Yes/No) [Yes]:

After this reboot

    reboot

I then had the instance come back up working as before. Next step is to 1.1.8 for configuration reasons.

    add system image https://downloads.vyos.io/release/legacy/1.1.8/vyos-1.1.8-amd64.iso
    What would you like to name this image? [VyOS-1.1.8]:
    Would you like to save the current configuration directory and config file? (Yes/No) [Yes]:
    Would you like to save the SSH host keys from your current configuration? (Yes/No) [Yes]:

Again reboot and I was ready for the final upgrade which is now covered in the [current VyOS documentation][vyos-docs]
and like the previous steps the defaults for the questions should be just fine for most cases.

    add system image https://downloads.vyos.io/rolling/current/amd64/vyos-rolling-latest.iso
    Do you want to continue without signature check? (yes/no) [yes]
    What would you like to name this image? [1.3-rolling-202004070629-]:
    Would you like to save the current configuration directory and config file? (Yes/No) [Yes]:
    Would you like to save the SSH host keys from your current configuration? (Yes/No) [Yes]:


At the end of this I had a fully upgraded router, I tested the traffic flowing through it as expected. Now of course
given no one had looked at this for six years the configuration is sadly not in code anywhere but thankfully
[Ansible][ansible] has support for configuring VyOS devices so the next step is getting these devices under code
control.

Peace, love and happiness.

[vyos]: https://www.vyos.io/
[vyos-old-docs]: https://wiki.vyos.net/wiki/Upgrade#Upgrading_from_pre-1.1.0
[vyos-docs]: https://vyos.readthedocs.io/en/latest/image-mgmt.html#update-vyos
[ansible]: https://www.ansible.com/
