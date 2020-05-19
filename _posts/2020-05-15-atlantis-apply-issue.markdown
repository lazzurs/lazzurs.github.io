---
layout: post
title:  "Atlantis apply issue"
date:   2020-05-15 12:29:00 +0000
categories: keyboard input
---
Not long after first setting up [Atlantis][atlantis] I noticed we had a problem with the more complex runs. In our
setup we are pretty much following the [Terragrunt example repo][terragrunt-example-repo] in that we have a repo full
of our custom modules and another repo that covers all of our environments and accounts. The custom modules are mostly
wrappers for the [Terraform AWS modules][terraform-aws-modules] and a few other community modules from the
[Terraform Registry][terraform-registry].

The more complex environments we have use a bunch of modules and it was running a [Terragrunt][terragrunt] apply-all
command over a whole enviroment that we were coming unstuck on with an error somewhat like the one below.

    Error: Error locking state: Error acquiring the state lock: ConditionalCheckFailedException: The conditional request failed
        status code: 400, request id: 0000
    Lock Info:
      ID:        00000000-c1a1-1015-dc75-9938775f0e33
      Path:      terragrunt/app/terraform.tfstate
      Operation: OperationTypeApply
      Who:       atlantis@eu-west-1.compute.internal
      Version:   0.12.24
      Created:   2020-05-14 17:51:58.576453175 +0000 UTC
      Info:

This was slightly weird as when I checked the DynamoDB for a lock it was not there and to be clear there were locks for
each directory that was running. It turns out this was a concurrency issue and was solved by adding the following to the
Terragrunt command line.

    --terragrunt-parallelism 1

I put this into the config for the Docker container and suddenly I was able to get the applies working for the
multiple module environments again, the larger apply-all runs of [Terragrunt][terragrunt].

Now of course this raises more questions. Why did this locking issue happen for one and how is anyone else running
[Atlantis][atlantis] and [Terragrunt][terragrunt] together before the above flag was released which only happened a few
days ago but those are for another day and another post.

Peace, love and happiness.

[atlantis]: https://www.runatlantis.io/
[terragrunt-example-repo]: https://github.com/gruntwork-io/terragrunt-infrastructure-live-example
[terraform-aws-modules]: https://github.com/terraform-aws-modules/
[terraform-registry]: https://registry.terraform.io/
[terragrunt]: https://terragrunt.gruntwork.io/
