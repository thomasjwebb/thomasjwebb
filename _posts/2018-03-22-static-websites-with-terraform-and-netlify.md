---
layout: post
title:  "Static Websites with Terraform, Netlify and NS1"
date:   2018-03-22 21:36:00 -0700
categories: netlify static terraform ns1 nsone 
---
I have been administrating servers since I was a teenager and I always have an NAS running at my place that I build that does various other things for me that warrant local presence. And I have some proxy servers I use to thwart the region locking by the ip trolls that run the entertainment industry. But I'm also getting to the point in my life where I want to do as little server administration as possible. Sure, administration has gotten quite a bit _easier_ over the years. But if I can avoid paying for ec2 instances, I will. If I can avoid having to worry about installing all the right versions of interpreters and libs on servers, I shall.

So like many people, I've moved a lot of things over to being static, using things like gatsby and jekyll to generate the static content locally and leverage the power of cdns and regional caching to speed up websites. One possible configuration for this is s3+cloudfront+route53. That last part, Amazon's DNS, is necessary unless you want to go back to the bad old days of having to type www before the domain name.

So my requirements for my static websites are as follows:

- Bare name must work and be the default
- https must work and I want to force https
- the costs must not balloon up if my traffic is low enough

So with these in mind, I went with netlify, at least for the static websites I'm starting out with. Netlify likes to access your github repos and it can build your static websites for you. That is awesome, however I couldn't find a way in its interface to specify subdirectories so I could put all my static sites in one repo. Maybe I could have deployed different branches to different sites, but that defeats the purpose of keeping it in one repo for me. So I used the manual deployment of building my site, going into its build or \_site subdirectory and uploading with netlify:

	netlify deploy

I then went into the interface and manually specified to use my domain for it. Terraform [seems to have support for netlify](https://github.com/mitchellh/terraform-provider-netlify) but as I don't see anything for its dns (which is in beta anyway), I went with the company netlify says they use for dns anyway, [ns1](https://ns1.com/). Terraform is awesome and lets me easily setup things like dns entries, web servers, etc. without the tedium of web-based interfaces. Really important as I actually have a lot of static websites to deal with and may have even more coming soon.

Terraform grabs all the terraform files (.tf) in your directory and applies them, so it's good to simply name the files based on what they do. As I'm only using it for dns for now, I just make a `ns1.tf` file in a directory for my terraform configuration, `infra` (doesn't matter what you call it). I could simply put one configuration in there like this:

```json
resource "ns1_zone" "example" {
  zone = "example.com"
}

resource "ns1_record" "example_web" {
  zone   = "example"
  domain = "example.com"
  type   = "ALIAS"
  ttl    = 60

  answers = {
    answer = "alias-domain.netlify.com."
  }
}

resource "ns1_record" "www_example_web" {
  zone   = "example"
  domain = "www.example.com"
  type   = "CNAME"
  ttl    = 60

  answers = {
    answer = "alias-domain.netlify.com."
  }
}

resource "ns1_record" "example_mail" {
  zone   = "example"
  domain = "example.com"
  type   = "MX"
  ttl    = 60

  answers = {
    answer = "1 aspmx.l.google.com."
  }

  answers = {
    answer = "5 alt1.aspmx.l.google.com."
  }

  answers = {
    answer = "5 alt2.aspmx.l.google.com."
  }

  answers = {
    answer = "10 aspmx2.googlemail.com."
  }

  answers = {
    answer = "10 aspmx3.googlemail.com."
  }
}
```

Replacing `alias-domain.netlify.com` with the actual netlify subdomain for your site, which you can get by clicking on its dns settings after entering your domain name in its config. Note that I have an ALIAS record here, a non-standard extention to allow a bare domain to effectively point to another external record's ip. Netlify also insists on having the www subdomain point to it, which you can simply use a CNAME for. Note that I'm also using gmail so I have the standard mx settings for that in here.

This is fine for just one site, but if you have multiple domains with pretty much the same configuration, modules make it so much easier. So I created a `modules` directory under my terraform configuration and under that, a `ns1_for_netlify` directory. So I put the same config as above, but modified to accept input in a file in that directory called `main.tf`:

```json
resource "ns1_zone" "example" {
  zone = "${var.domain}"
}

resource "ns1_record" "example_web" {
  zone   = "${ns1_zone.example.zone}"
  domain = "${ns1_zone.example.zone}"
  type   = "ALIAS"
  ttl    = 60

  answers = {
    answer = "${var.alias_domain}"
  }
}

resource "ns1_record" "www_example_web" {
  zone   = "${ns1_zone.example.zone}"
  domain = "www.${ns1_zone.example.zone}"
  type   = "CNAME"
  ttl    = 60

  answers = {
    answer = "${var.alias_domain}"
  }
}

resource "ns1_record" "example_mail" {
  zone   = "${ns1_zone.example.zone}"
  domain = "${ns1_zone.example.zone}"
  type   = "MX"
  ttl    = 60

  answers = {
    answer = "1 aspmx.l.google.com."
  }

  answers = {
    answer = "5 alt1.aspmx.l.google.com."
  }

  answers = {
    answer = "5 alt2.aspmx.l.google.com."
  }

  answers = {
    answer = "10 aspmx2.googlemail.com."
  }

  answers = {
    answer = "10 aspmx3.googlemail.com."
  }
}
```

We'll also need a way to get those vars in there, so I created another file called `vars.tf` in the same directory:

```json
variable "alias_domain" {
  description = "The address to point the ALIAS bare and CNAME www records to"  
}

variable "domain" {
  description = "The domain to serve"
}
```

Now back in our ns1.tf file, I'm able to easily create multiple websites. Note that you'll also need to generate an api key for your nsone.com account and put it in here:

```json
provider "ns1" {
  apikey = "API_KEY"
}

module "example" {
  source = "modules/ns1_for_netlify"

  domain = "example.com"
  alias_domain = "blahblahblah.netlify.com."
}

module "fubar" {
  source = "modules/ns1_for_netlify"

  domain = "fubar.biz"
  alias_domain = "whoopwhoopwhoop.netlify.com."
}
```

When using modules, you'll need to call `terraform get` before running `terraform apply`. Since I had previously used the more tedious approach before switching to using modules, the first time I ran `terraform apply` succeeded in deleting the old config but failed in recreating essentially the same config because the old config was still in the process of being deleted. Waiting a few seconds and trying again, it succeeded. You shouldn't have issues if you use modules from the get-go but just letting you know not to freak out if it does fail at that step. Just wait and try again.

After applying this, I went back into the netlify config for each of the domains, and added https which it can't do until the dns is right, then after testing I turned on force ssl. I think it's good to get everything on ssl these days. I tested the websites relentlessly and tested sending e-mails to each of the domains to make sure.