---
layout: post
title:  "Verifiable build chain in homebrew"
date:   2020-02-13 12:00:00 +0000
categories: homebrew aws open-source security
---
Yesterday I did some work on a [pull request][homebrew-cask-pr] for [Homebrew Cask][homebrew-cask] to add the new 
version 2 of the awscli to the package manager. This pull request did not get accepted and I figured it was worth 
writing why I think this is a good thing.

The reason for rejection was that while AWS had changed their distribution model for the CLI the source was still 
available. [Homebrew][homebrew] has a policy of always wanting to build packages from source were possible. The reason
for this policy is clear, they want to provide their users a verifiable build chain.

A verifiable build chain allows users of the packaging tool to see exactly where the source comes from and how it is
built into the package that is used on their system in a way that they can also package themselves. This is useful in 
the software world to ensure that no one is adding anything into a build to compromise your system.

Now of course no one using the awscli thinks that AWS is adding anything into the binary package they are distributing
that would be designed to compromise our systems, if we were why would we use AWS in the first place. That trust however
cannot be verified which is why [Homebrew][homebrew] have insisted on building from source.

Software is not the only place where this applies of course, supply chains matter in the hardware world and they also
matter in the food industry where this would usually be referred to by terms like "Field to plate" to describe the 
process food goes through from the seed and soil to get to your plate and as much as what food you eat will change 
your system the same is true of the software you consume.

To fix this I created a [pull request][homebrew-core-pr] for [Homebrew][homebrew] which was accepted this morning.
I have archived my [homebrew cask tap][awscli-cask-tap] so that other people can see it and fork it if they would 
rather use the distribution from AWS.

Peace, love and happiness.

[homebrew]: https://brew.sh/
[homebrew-cask]: https://github.com/Homebrew/homebrew-cask
[homebrew-cask-pr]: https://github.com/Homebrew/homebrew-cask/pull/76967
[homebrew-core-pr]: https://github.com/Homebrew/homebrew-core/pull/50083
[awscli-cask-tap]: https://github.com/lazzurs/homebrew-awscli
