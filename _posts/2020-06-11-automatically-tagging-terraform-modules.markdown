---
layout: post
title:  "Automatically tagging terraform modules"
date:   2020-06-11 00:00:00 +0000
categories: terraform git jenkins bitbucket
---
Following the [example Terragrunt modules repo][example-terragrunt-modules-repo] in the new current place I am in the first 
thing I did was to create a shared modules repo to fit with our environment repo which is based loosely on the 
[example Terragrunt environments repo][example-terragrunt-environments-repo]. The idea with this is anything we need is a
Terraform module. That repo then gets used by the environments repo and allows deployment of many copies of any module.

With this however comes the requirement to get a copy of a module at a specific point in time. Now given the modules repo
is in git one easy way would be just to use git commit referencies but those are fairly unfriendly. A far nicer way of 
having consistant referencies in the repo would be to have versions and for this I decided to follow 
[semantic versioning][semver]. The thinking behind this was to have any standard change be a patch, any new module would be a 
minor version and then the major version being bumped from 0 to 1 would be when we had full control of our stack. This was all 
being done for an infrastructure that was manually created in AWS via the web console to start with.

As with all good things for the first few releases this was done manually and then automated. I am a big fan of the saying 
"Once, Twice, Automate" and so that's exactly what I did here. I tried out tagging the repo, I wrote into the README for the
repo how the tagging should be done and then I looked into how to easily automate this.

For our sins we are using [Jenkins][jenkins] as our CI tool and so that seemed like the obvious place to do this release tagging
of the repo with the intention further automation could be done around pull requests to test code and so on. To do this I threw 
a Jenkinsfile in the repo and along with [semver-tool][semver-tool] was able to quickly get the tagging I had been doing manually
automated.

Here's the science bit...

```
pipeline {
   agent { label 'standard-agent-label' }

   stages {
      stage('Bump version') {
         steps {
            withCredentials([
                usernamePassword(credentialsId: 'jenkins-git-creds',
                    usernameVariable: 'GIT_USERNAME',
                    passwordVariable: 'GIT_PASSWORD'
                )
            ]) {
                sh '''
                #!/usr/bin/env bash
                echo "Getting merge commit message"
                git_merge_log=$(git log -1)
                if `echo $git_merge_log | grep -q '#major-yes-i-know-what-i-am-doing'`; then
                  export ver_bump="major"
                elif `echo $git_merge_log | grep -q '#minor'`; then
                  export ver_bump="minor"
                else
                  export ver_bump="patch"
                fi
                echo "Getting semver-tool"
                curl https://raw.githubusercontent.com/fsaintjacques/semver-tool/master/src/semver -o semver
                current_version=$(git describe --tags --abbrev=0)
                echo $current_version
                new_version=$(bash semver bump $ver_bump $current_version)
                echo $new_version
                git config --global user.email "automation@example.com"
                git config --global user.name "Jenkins Automation"
                git checkout main
                git tag -a v$new_version -m "Automated tag update"
                git config --local credential.helper "!p() { echo username=\\$GIT_USERNAME; echo password=\\$GIT_PASSWORD; }; p"
                git push --follow-tags
                '''
            }
            script {
              def new_tag = sh(returnStdout: true, script: 'git describe --tags `git rev-list --tags --max-count=1`').trim()
              currentBuild.displayName = new_tag
              office365ConnectorSend message: "New tag in infrastructure-modules: $new_tag", webhookUrl:'https://example.com/webhookid'
            }
         }
      }
   }
}

```

From the above it went through a few versions, the first major change was to major. As we want to be very sure that when we go to version 1.0.0
it's because we mean it.

Second was to make sure that no matter what the latest tag is what is used rather than any tag in the repo. We had a small issue when someone
manually added a tag and the versioning ended up going further back than expected.

With the above now all of the versioning of the modules repo is automated on a webhook from the Bitbucket server to Jenkins and then single 
tags can referred to in the terragrunt.hcl in each environment in the environments repo.

Peace, love and happiness.

[example-terragrunt-modules-repo]: https://github.com/gruntwork-io/terragrunt-infrastructure-modules-example
[example-terragrunt-environments-repo]: https://github.com/gruntwork-io/terragrunt-infrastructure-live-example
[semver]: https://semver.org/
[semver-tool]: https://github.com/fsaintjacques/semver-tool
[jenkins]: https://www.jenkins.io/
