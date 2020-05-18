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

First attempt at this I had a little shell script start the container and it then used a bunch of environment vars
to populate the atlantis command line. This is not the correct way to do things as I later learned.

    URL="https://somethingsomething.ngrok.io"
    SECRET="secretsecret"
    TOKEN="complextoken"
    USERNAME="git-user-name"
    REPO_WHITELIST="our.bitbucket.server/DevOps/infrastructure-environments"
    BASE_URL="https://our.bitbucket.server/bitbucket/"
    OPSGENIE_API_KEY="opsgenie-api-key-value"

    docker run -it -p 4141:4141 \
    -v /Users/lazzurs/tmp/atlantis/:/tmp/repos/ \
    -v /Users/lazzurs/tmp/atlantis/ssh/:/home/atlantis/.ssh/ \
    -e GIT_SSH_COMMAND="ssh -i /home/atlantis/.ssh/id_rsa -o 'StrictHostKeyChecking no'" \
    -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID \
    -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY \
    -e AWS_SESSION_TOKEN=$AWS_SESSION_TOKEN \
    -e OPSGENIE_API_KEY=$OPSGENIE_API_KEY \
    lazzurs/atlantis-terragrunt atlantis server \
    --atlantis-url="$URL" \
    --bitbucket-user="$USERNAME" \
    --bitbucket-token="$TOKEN" \
    --bitbucket-webhook-secret="$SECRET" \
    --bitbucket-base-url="$BASE_URL" \
    --repo-whitelist="$REPO_WHITELIST" \
    --repo-config=/tmp/repos/repos.yaml

This however allowed me to get up and running with a basic test using [ngrok][ngrok] to see if Atlantis would work with
our setup and what else I might need to do to get it working. The biggest problem at this point was going to be our git
server requiring an SSH key to get the repo and have permissions over it. As you can see from the above script I solved
this locally by just mounting in an SSH key. For the later setup in ECS this was more of a pain.

A better version of this came later when I re-read the [Atlantis Docs][atlantis-docs] I realised that all of those
command line arguments can also be provided as environment variables.

Peace, love and happiness.

[atlantis]: https://www.runatlantis.io/
[terragrunt]: https://terragrunt.gruntwork.io/
[atlantis-container-source]: https://github.com/lazzurs/docker-atlantis-terragrunt
[atlantis-container-build]: https://travis-ci.org/github/lazzurs/docker-atlantis-terragrunt
[docker-hub]: https://hub.docker.com/
[atlantis-terragrunt-container]: https://hub.docker.com/repository/docker/lazzurs/atlantis-terragrunt
[ngrok]: https://ngrok.com/
[atlantis-docs]: https://www.runatlantis.io/docs/
