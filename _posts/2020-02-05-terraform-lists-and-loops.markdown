---
layout: post
title:  "Terraform lists and loops"
date:   2020-02-05 14:45:00 +0000
categories: terraform
---
[Terraform][terraform] is a great tool. I like it because it takes all of the cloud providers I might want to use and gives me one common interface in code.

This is a hard thing to do and it is in no way perfect at doing this. I think one of the big mistakes in the original design was trying to flatten all of the complexity into a configuration language rather than accepting that things like conditionals and loops would be required and as Terraform has evolved it has had to shoehorn these features into the flat configuration language.

Desired state, well that can be a post for another day. Oh the joys of the state files and editing them.

The fun I am having today is dealing with existing manually created infrastructure and having to create some new things in that infrastructure. When I do things I do them right and so I am not going to just go click buttons in the AWS web console, I am going to write them in code. Terraform thankfully has some good support for doing this with data lookups.

Here I am creating an ECS cluster in an existing VPC with an existing subnet. Given I always re-use code when I can I am using [Terraform community modules][terraform-community-modules] rather than writing a bunch of my own code, for the most part this makes sense and makes my day easier allowing my work to get done quicker. Let's get to the code...



    //This looks fine
    data "aws_subnet_ids" "public_subnet_ids" {
      vpc_id = var.vpc_id
    
      tags = {
        Name = "PublicSubnetTagName"
      }
    }

    //Have to loop because it is a set and not a list returned by aws_subnet_ids
    data "aws_subnet" "public_subnets" {
      for_each = data.aws_subnet_ids.public_subnet_ids.ids
      id       = each.value
    }

    module "something-service-http" {
      source  = "terraform-aws-modules/security-group/aws"
      version = "3.3.0"
    
      name        = "something-http"
      description = "Security group for something"
      vpc_id      = var.vpc_id
    
      ingress_with_cidr_blocks = [
        {
          from_port   = 8080
          to_port     = 8080
          protocol    = "TCP"
          description = "HTTP"
          //WTFOMGPONIEZ
          cidr_blocks = join(",",[for s in data.aws_subnet.public_subnets : s.cidr_block])
        }
      ]
    }

Pulling the subnet IDs and then getting subnet objects from that makes sense to me. Where this all starts to fall down is using the [set][terraform-set] type in Terraform rather than a [list][terraform-list]. A [set][terraform-set] does make some sense as you never are going to have more than one of each subnet ID but there are far less options when it comes to what you can do with a set in comparison to a [list][terraform-list]. This leads to the loop inside the aws_subnet lookup which is fine but finally the module for security groups takes as an input a string of CIDR blocks with a comma between each one rather than a [list][terraform-list]. This is a mess of types where really lists would have been ideal all the way through.

Ah well, it works but I don't envy anyone having to do the same.

Peace, love and happiness.

[terraform]: https://www.terraform.io/
[terraform-set]: https://www.terraform.io/docs/configuration/types.html#set-
[terraform-list]: https://www.terraform.io/docs/configuration/types.html#list-
[terraform-community-modules]: https://registry.terraform.io/
