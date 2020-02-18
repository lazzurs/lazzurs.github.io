---
layout: post
title:  "Packer base images"
date:   2020-02-14 12:00:00 +0000
categories: packer hardening security aws
---
Today I have been busy working on a fairly typical task when starting in a new place, a [Packer][packer] base image.

[Packer][packer] is a great tool for building images for a bunch of providers including in my case [AWS][packer-aws] but
 I have used it in the past for [Azure][packer-azure], [GCP][packer-gcp], [VMWare][packer-vmware],
 [Vagrant][packer-vagrant], [Docker][packer-docker] and others. This really shows the power of this tool to create
 consistent base images no matter what technology you are using for your compute or what you want to use next.00

Packer has a concept of [builders][packer-builders] where you can build in a specific way depending on the compute
technology you are using. There is then [provisioners][packer-provisioners] which allow you to configure that image
which supports all of the common configuration management tools and allows you to take your existing configuration
management code to build your image. Finally there is [post-processors][packer-post-processors] plugins that allow
you to take the result of your work and process it in different ways.

The [AWS][packer-aws] builder I usually use is the [EBS][packer-aws-ebs] builder. It is very simple, it takes an
existing AMI and runs provisioners on the system and saves this as a new AMI. For our base image this was exactly
what we needed, to be able to take Amazon Linux 2 and apply some simple Ansible roles to do some basic security
hardening of the image and then save this as an AMI that our application teams could use to build their application
AMIs to then be used in auto-scaling groups.

As I mentioned at the start this is a fairly standard task in any new user of cloud providers as they will want a base
image that meets their own needs and security requirements and maybe sets things up to follow their standard deployment
pattern. I use [Packer][packer] for this every time as it allows re-using the existing configuration management code
they have and means we can apply the same work to their [Vagrant], [Docker] or multiple cloud provider images in the
same way.

Peace, love and happiness.

[packer]: https://packer.io/
[packer-aws]: https://packer.io/docs/builders/amazon.html
[packer-azure]: https://packer.io/docs/builders/azure.html
[packer-gcp]: https://packer.io/docs/builders/googlecompute.html
[packer-vmware]: https://packer.io/docs/builders/vmware.html
[packer-vagrant]: https://packer.io/docs/builders/vagrant.html
[packer-docker]: https://packer.io/docs/builders/docker.html
[packer-builders]: https://packer.io/docs/builders/index.html
[packer-provisioners]: https://packer.io/docs/provisioners/index.html
[packer-post-processors]: https://packer.io/docs/post-processors/index.html
