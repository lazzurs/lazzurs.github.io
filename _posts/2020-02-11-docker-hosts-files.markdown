---
layout: post
title:  "Docker hosts files"
date:   2020-02-11 12:00:00 +0000
categories: docker ecs 
---
Moving legacy infrastructure to Docker is fun. It is always great to explain to people we can take a little snowflake of a system and split up everything it did into individual components that are predictable and can use only the resources they need.

In this specific example I am taking [Jenkins][jenkins] from a server that had a bunch of other things on it. This was in AWS and the server is a m4.2xlarge and has a 1.2Tb EBS volume attached because of reasons. The costs are not exactly small and yet the resource usage for the most part is and the Jenkins master requires a tiny fraction of those resources.

Given this is on an instance there have been a bunch of custom and manual things done to that instance over time and one of them is entries in the /etc/hosts file, in this case just over 300 entries. In a Docker container you cannot just replace the /etc/hosts file as it is generated on the startup of the container. Docker has two solutions for this, one is to specify on the command line extra hosts to add but with over 300 entries this is not a good idea. The other tool that Docker provides is to specify a search domain and ECS also supports this in [task definitions][ecs-task-definition].

What this allows me to do is add all of the entries in the hosts file into a Route 53 private zone and then set the search domain to the name of the zone. With this in place every hostname lookup in the container will first check for the entry in the domains listed in the search domains first.

To quickly create the zone entries and get them managed in code I used the following awful awk single line.

    awk '/^[^#]/ {print "resource \"aws_route53_record\" \""$2"\" {\n zone_id = var.hosts_zone_id\nname    = \""$2"\"\ntype    = \"A\"\nttl     = \"300\"\nrecords = [\""$1"\"]\n}"}' hosts | pbcopy

That gave me all of the entries I needed in Terraform to re-create the local hosts file in the Route 53 private zone and put them into the paste buffer so I could paste it straight into a file for my IDE (PyCharm) to tidy up. 

To support all of this I had to make changes to my [ECS service module][terraform-ecs-service-module] to support the DNS search domains in the task definition.

Update:

I had some further fun with this.

First off I missed adding DNS search domains does not work with awsvpc mode networking for a service. That is fine for this case I can switch the service back to using the bridged mode. Thankfully this then showed up an error in my module in the case of not using awsvpc mode where I had not specified the default value for the networking configuration for the task correctly and allowed me to fix this.

However I then hit into this error.

    Error: ClientException: DNS search domain hosts.bah.moo.woo.quack.fsck. contains invalid characters, does not match pattern ^[a-zA-Z0-9-.]{0,253}[a-zA-Z0-9]$

Now you will notice at the end of the domain name there a . which is technically correct. AWS tends to provide the dot at the end of domains from Route 53 when querying the API and I have hit this before. Sadly ECS will not accept a domain with a dot at the end when specifying search domains. Being technically correct in one service and not in another is annoying.

Peace, love and happiness.

[ecs-task-definition]: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html
[jenkins]: https://jenkins.io/
[terraform-ecs-service-module]: https://registry.terraform.io/modules/lazzurs/ecs-service/aws/
