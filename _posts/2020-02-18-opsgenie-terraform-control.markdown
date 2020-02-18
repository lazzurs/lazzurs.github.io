---
layout: post
title:  "Opsgenie Terraform control"
date:   2020-02-18 12:00:00 +0000
categories: opsgenie terraform iac
---
I want to automate all the things. Pretty much anything I do can usually be done quicker and better by a machine and
usually one controlled from some simple code.

In this case I have been setting up [Opsgenie][opsgenie] for the first time here. We selected [Opsgenie][opsgenie]
because we needed an on-call management and alerting tool that was external to our infrastructure and while the
usual kid on the block I would look to first is [Pagerduty][pagerduty] this specific client happens to already be
all in with the [Atlassian][atlassian] toolset so [Opsgenie][opsgenie] made more sense and it also happens to be
cheaper for the features we want which is no small thing.

Thankfully there is a [Terraform provider for Opsgenie][terraform-opsgenie] which is well documented and does everything
it appears we are going to need to do. We had an initial play with with the tool to see what we might want to do with it
and see how it works so we already had a few users in there with some teams defined but thankfully the
[Terraform][terraform] resources in this case support import. With Terraform since it manages a view of the state of
which ever service it is managing if you already have created resources with another tool you can use
[terraform import][terraform-import] to bring the current state of those resources into the Terraform state file.
After this initial import the resources can be managed by code.

This process mostly went smoothly other than for my own user. For all the other users the ID as mentioned in the
[terraform opsgenie user resource][terraform-opsgenie-user-resource] appears in the web interface but for my own user
it did not. Given I needed my ID to import my user I had to find someway to get this. Thankfully the
[Opsgenie API][opsgenie-api] that [Terraform][terraform] is using to speak with [Opsgenie][opsgenie] supports
[querying a user][opsgenie-user-api] and I was able to get my ID and import myself into the state file, I now existed
in code.

At the end of this I am not sure how much of this service we really want to manage in code. The users are a clear
advantage as git will provide an audit trail for adding or modifying users and the same is true of teams but it
may be that the schedules will be better managed in the web interface as will the contact details for users for user
self management. While automation and Infrastructure as Code is a great thing it is important to know when to apply
and when sometimes it does not make sense.

Peace, love and happiness.

[opsgenie]: https://www.atlassian.com/software/opsgenie
[pagerduty]: https://www.pagerduty.com/
[atlassian]: https://www.atlassian.com/
[terraform-opsgenie]: https://www.terraform.io/docs/providers/opsgenie/index.html
[terraform-import]: https://www.terraform.io/docs/import/index.html
[terraform-opsgenie-user-resource]: https://www.terraform.io/docs/providers/opsgenie/r/user.html
[opsgenie-user-api]: https://docs.opsgenie.com/docs/user-api#section-get-user
[opsgenie-api]: https://docs.opsgenie.com/docs/api-overview
[terraform]: https://www.terraform.io/
