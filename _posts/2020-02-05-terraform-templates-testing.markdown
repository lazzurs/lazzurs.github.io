---
layout: post
title:  "Terraform templates testing"
date:   2020-02-05 17:45:00 +0000
categories: terraform
---
Today I hit a problem with my [Terraform ECS module][terraform-ecs-module] where I needed a new ECS cluster to use a HTTP proxy in the network it was being deployed to. I am generally a fan of outgoing firewalls and in AWS since you will need services like ECS to access internet services having an HTTP proxy in the way of outgoing traffic is a great way of making your environment more secure.

In doing this I had to add some further configuration to the instance user data. The module previously had this split as a switch but thanks to the lack of case statements in [Terraform][terraform] it make more sense to put the switch of EFS/HTTP proxy support in the template, it also makes it easier to combine EFS and HTTP proxy support.

In doing so I needed to test the new template, I could have done this in the module but that would have required spinning up clusters just to test a file template. Instead I created a temporary directory with the template file and the following [Terraform][terraform]

    variable "ecs_name" {
      description = "ECS Cluster Name"
      type        = string
      default = "rl-ecs-cluster"
    }
    
    variable "efs_id" {
      description = "The EFS ID - Required if attach_efs is true"
      type        = string
      default     = "efs-id"
    }
    
    variable "http_proxy" {
      description = "Name of the HTTP proxy on the network"
      type        = string
      default = "proxy"
    }
    
    variable "http_proxy_port" {
      description = "Port number of the HTTP proxy"
      type        = number
      default     = 3128
    }
    
    data "template_file" "user_data" {
      template = file("./user_data.tpl")
      vars = {
        ecs_cluster_name = var.ecs_name
        efs_id = var.efs_id
        http_proxy = var.http_proxy
        http_proxy_port = var.http_proxy_port
      }
    }
    
    output "user_data_file" {
      value = data.template_file.user_data.rendered
    }

In the same directory I placed the test template file and then ran 

    terraform init
    terraform apply

Each time I needed to make a change to the code or template I could quickly run this and test locally. Simple.

Peace, love and happiness.

[terraform]: https://www.terraform.io/
[terraform-ecs-module]: https://registry.terraform.io/modules/lazzurs/ecs/aws/
