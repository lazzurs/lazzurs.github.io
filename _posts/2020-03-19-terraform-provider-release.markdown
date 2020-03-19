---
layout: post
title:  "Terraform provider release"
date:   2020-03-19 00:00:00 +0000
categories: terraform providers opensource
---
I have had the pleasure recently of working on the [terraform icinga2 provider][terraform-icinga2-provider] as I use it on one of my projects. A while back I did my first pull request and as I want it to have full control over the Icinga2 API I offered to do more work on the provider and this was accepted.

First off was getting the Github project cleaned up, resolving all of the pull requests and doing some basic README updates to make the project look a little more loved.

Next was making a release and this is where things came a little unstuck. As with most mistakes this one started with an assumption. I assumed that [Terraform][terraform] providers worked just like modules. For a module that gets released on the [Terraform registry][terraform-registry] all you have to do is configure the registry to watch your repo and then put a version tag on the repo and boom the module is shown as a release on the registry.

Providers on the other hand are published on [Hashicorp releases][hashicorp-releases] which has a different process. Thankfully Hashicorp were super helpful with this. I figured I should document here what is required.

1. Get all the changes made you want in the next release on the master branch
2. Update the CHANGELOG.md with the new version and after this `(Unreleased)` with any sections
3. Let Hashicorp know a new release needs done in the Hashicorp Slack.
4. Wait for the tf-release-bot to do the awesome and make the release happen.

For this to happen I had to undo the work I had done in pressing the release button in Github. For some reason there wasn't a delete button on the release page so to delete this I had to delete the tag from the repo and this turned the release into a draft which I could then delete.

Peace, love and happiness.

[terraform]: https://www.terraform.io/
[terraform-icinga2-provider]: https://github.com/terraform-providers/terraform-provider-icinga2
[terraform-registry]: https://registry.terraform.io/
[hashicorp-releases]: https://releases.hashicorp.com/
