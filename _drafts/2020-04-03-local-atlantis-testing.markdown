---
layout: post
title:  "Local Atlantis testing"
date:   2020-04-03 12:00:00 +0000
categories: atlantis terragrunt terrafrom
---
I have been helping to roll out [Atlantis][atlantis] to our team to provide some Continuous Integration for our new
[Terragrunt][terragrunt] based infrastructure management. A friend pointed it out and ever since I have been fairly
impressed at how simple it is and how well it is works.

Since we are using Terragrunt the first thing I had to do was create an [Atlantis][atlantis] container with
[Terragrunt][terragrunt] loaded in. This seemed like something that would be useful to everyone else so I published the
[container source][atlantis-container-source] and setup an [automated-build][atlantis-container-build] and had the build
publish the container to [Docker Hub][docker-hub] [here][atlantis-terragrunt-container].

Once I had all of this ready I was ready to run Atlantis locally to test the configuration I wanted to deploy. This is
suggested in the [Atlantis quick start documentation][atlantis-quick-start-docs] and for me it was useful before
deploying into our ECS cluster.

Peace, love and happiness.

[atlantis]: https://www.runatlantis.io/
[terragrunt]: https://terragrunt.gruntwork.io/
[atlantis-container-source]: https://github.com/lazzurs/docker-atlantis-terragrunt
[atlantis-container-build]: https://travis-ci.org/github/lazzurs/docker-atlantis-terragrunt
[docker-hub]: https://hub.docker.com/
[atlantis-terragrunt-container]: https://hub.docker.com/repository/docker/lazzurs/atlantis-terragrunt
