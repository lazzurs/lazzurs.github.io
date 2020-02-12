---
layout: post
title:  "Homebrew cask tap for awscli"
date:   2020-02-12 13:00:00 +0000
categories: homebrew aws
---
Our good friends have released the new version of the awscli. This is good news and the timing could not be better as the team I am working in is about to switch to using AWS SSO. With the new release there is a complete change in the way it has been packaged. Before it was a python package that could be installed via pip and other package managers used this but it was dependant on the system python. Now the cli comes bundled in an operating system package. This however has broken the model used for managing this package in the popular macOS (and now Linux) package manager [Homebrew][homebrew]

Homebrew does have a model for dealing with binary packages called [Casks][homebrew-cask] and given this is now a binary package this is how it should be handled. I have created a [pull request][homebrew-cask-pr] here for just this.

One of the things I really like about [Homebrew][homebrew] is how easy it is to create your own package repositories and use them and in this case I did just that as I need the new CLI on my machine today. These are called [Homebrew Taps][homebrew-tap] and mine for the awscli cask is [here][awscli-cask-tap]. The steps for creating this were simple:

* Create a repo on github with the name starting homebrew-
* Add a directory to the repo, Formula for brew formulas and Casks for casks
* Add the formula or casks to the directory
* Profit (I wish)

To use the tap I have created from the command line do the following

    brew tap lazzurs/awscli
    brew cask install lazzurs/awscli/awscli

Or if you use [Brewfile][brewfile]

    tap 'lazzurs/awscli' || true
    cask 'lazzurs/awscli/awscli'


Peace, love and happiness.

[homebrew]: https://brew.sh/
[homebrew-cask]: https://github.com/Homebrew/homebrew-cask
[homebrew-cask-pr]: https://github.com/Homebrew/homebrew-cask/pull/76967
[homebrew-tap]: https://docs.brew.sh/How-to-Create-and-Maintain-a-Tap
[awscli-cask-tap]: https://github.com/lazzurs/homebrew-awscli
[brewfile]: https://github.com/Homebrew/homebrew-bundle
