---
layout: post
title:  "Packer App image Part 1"
date:   2020-02-19 12:00:00 +0000
categories: packer devops
---
Today I have started working on the first application [Packer][packer] image. The idea is we have a base image in
[Packer][packer] that is configured with all of the security hardening and any other custom configuration we need in
all of our images that I wrote about [here][packer-base-image-post] and then build on this image for each application.

The application will be built via CI, [Jenkins][jenkins] in our case, and then once the application is built it will be
packaged into an AMI via [Packer][packer]. This AMI can then be used in the autoscaling groups to automatically spin up
the image.

## Historical context

Previously here they had been creating static instances and configuring each of those with [Ansible][ansible] so I was
lucky enough to already have some configuration management code to use with both the [Packer][packer] base image and
the new application image. This change from the configuration being applied to each instance via Ansible to being
built into the images changes where the environment specific information can live. Previously this had been in the
[Ansible][ansible] role for the application but now [Ansible][ansible] is being used in the Packer build the
configuration differencies for each environment need to be put in a different way. Thankfully there is a great tool for
this called [cloud-init][cloud-init] which is a standard across many cloud providers.

## Configuration

With [cloud-init] you can specify as part of your virtual machine startup a number of actions the virtual machine
should perform to get ready for service. This means anything that changes per environment say like a database password
or the hostname for a load balancer can be provided to the application as the instance starts up. In our case we use
[Terraform][terraform] for managing the infrastructure so what happens is this. Packer grabs an application specific
Ansible role which has the configuration templates in that developers can update as they make changes to the
application. [Packer][packer] grabs this role and puts the templates into the virtual machine. [Cloud-init][cloud-init]
then loads these templates and replaces the environment specific values in the template and puts the configuration file
in place for the application to use.

Doing this ended up being slightly more involved than I had initially anticipated. Cloud-init does have support for
templates it was not as trivial as I had hoped to take variables from AWS SSM and put them into a template. Below is an
example of how I got this working based in part from an [example on Stackoverflow][stackoverflow-example]

    Content-Type: multipart/mixed; boundary="==BOUNDARY=="
    MIME-Version: 1.0
    --==BOUNDARY==
    Content-Type: text/cloud-config; charset="us-ascii"
    ---
    runcmd:
    - export URL=${var.url}
    - export HSM_ENABLED=${var.hsm_enabled}
    - export API_KEY=$(aws ssm get-parameter --name "${var.api_key}" --with-decryption --region ${var.aws_region} --output text --query 'Parameter.Value')
    - readonly PYTHON_JINJA2="import os; import sys; import jinja2; sys.stdout.write(jinja2.Template(sys.stdin.read()).render(env=os.environ))"
    - cat /srv/app/conf_tpl/application.properties.j2 | python -c "$$${PYTHON_JINJA2}" > /srv/app/config/application.properties
    --==BOUNDARY==--

The above was added to the user data template which on boot up will set some environment variables that are brought in
from Terragrunt and others pulled directly from AWS SSM as they are encrypted secrets. These environment variables are
then used by [Jinja2][jinja2] to replace values in the templated configuration file.

All together this allows an image to be built with a templated configuration for any service which then on boot up will
configure the service based on specific configuration placed into that environment by [Terragrunt][terragrunt] and
secrets based in the AWS SSM.

Peace, love and happiness.

[packer]: https://packer.io/
[packer-base-image-post]: https://unicornops.lazzurs.ie/packer/hardening/security/aws/2020/02/14/packer-base-images.html
[ansible]: https://www.ansible.com/
[cloud-init]: https://cloud-init.io/
[stackoverflow-example]: https://stackoverflow.com/questions/25862071/accessing-os-environment-variables-from-jinja2-template
[jinja2]: https://jinja.palletsprojects.com/
[terragrunt]: https://terragrunt.gruntwork.io/