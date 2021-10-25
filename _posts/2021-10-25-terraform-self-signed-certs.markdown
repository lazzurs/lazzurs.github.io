---
layout: post
title:  "Terraform self signed certs"
date:   2020-02-01 17:47:28 +0000
categories: terraform tls certificates
---
Today I am solving the problem of setting up a mutual TLS gateway to replace a VPN. As part of this I have to do some 
basic certificate generation and management and I figured since everything else is in Terraform here that using the 
TLS provider was the right way forward for this.

Doing this is fairly easy, below is an example for a fully self signed certificate.

```hcl
resource "tls_private_key" "this" {
  algorithm = "RSA"
  rsa_bits  = 4096
}

resource "tls_self_signed_cert" "this" {
  allowed_uses = [
    "digital_signature",
    "server_auth",
    "client_auth"
  ]
  key_algorithm         = tls_private_key.this.algorithm
  private_key_pem       = tls_private_key.this.private_key_pem
  validity_period_hours = 17520 # 2 Years
  subject {
    common_name  = "bah.example.com"
    organization = "Example Corp."
  }
}
```

Now it would be remiss of me not to point out that Terraform does not recommend the user of tls_private_key in 
production deployments because the private key will be stored in the state file. I would suggest you likely 
already have secrets in there if you are using Terraform for other things. What I would say is if your usage is anything
beyond the above you might want to look into a full certificate server.

Peace, love and happiness.