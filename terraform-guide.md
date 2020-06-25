# Terraform: An Opinionated Guide

## Resources

1. [Terraform Tutorials](https://learn.hashicorp.com/terraform)
2. [Terraform: Up and Running](https://www.terraformupandrunning.com/)
   by Yevgeniy Brikman, 2nd Edition (2019)
   ([Code](https://github.com/brikis98/terraform-up-and-running-code))
3. [Terraform Docs](https://www.terraform.io/docs/providers/index.html)

Terraform docs for resource/data directives -- use them. Every *resource* doc
with a `/r/` path also has a *data* doc by replacing `/r/` with `/d/`:

* [Resource: aws_instance][r_instance]
* [Data: aws_instance][d_instance]

[r_instance]: https://www.terraform.io/docs/providers/aws/r/instance.html
[d_instance]: https://www.terraform.io/docs/providers/aws/d/instance.html


## Overview

Terraform's value proposition is clear: write configuration files to make data
center provisioning both automated and repeatable; track these files with
version control to have declarative infrastructure as code.

Terraform's design decidedly passes through the underlying resource concepts.
When writing a .tf file, you think in terms of resources provided by the vendor
(e.g. the various AWS services). This has the advantage of being able to refer
to vendor documentation and control panels (e.g. AWS Console web UI) as you
draft and revise configuration.

This pass-through abstraction model, however, isn't an abstraction of
infrastructure itself. If you want to say "provision a server that runs a web
server, put it on a private subnet, and terminate HTTPS with a load balancer,"
you say it by writing configuration directives for each of the 20 resources
made available by the provider (e.g. AWS).

Terraform's unit of composition is its module, but modules are inherently
shallow given the natural pressure to proliferate non-default variables in
order to accurately specify infrastructure. Ideally, composition is
[deep](https://www.youtube.com/watch?v=bmSAYlu0NcY&t=784), hiding away as much
underlying detail as possible for each required parameter. Modules have their
place in Terraform, especially for in-house reuse, but they do not lend
themselves to a catalog of common patterns because you have to read--and often
rewrite--each line of the module. Modules are shallow indeed.

Such shallow abstraction of infrastructure creates a void to be filled by a
next generation of tools with deep abstraction: data-driven configuration files
that say "provision a server that runs a web server, put it on a private
subnet, and terminate HTTPS with a load balancer," without having to say
anything further. (Example: [Pulumi](https://www.pulumi.com/).) There's a
trade-off, however. In order to get that deeply abstracted configuration file,
you have to write the code that interprets it. It's in this trade space that we
see Terraform is still an optimal tool, the incumbent candidate in writing
infrastructure as code, and the natural choice when you already think in terms
of resources provided to you by your vendor.


## Guidance

*Opinions to accelerate putting Terraform into production.*

**State.** Best to start with a single dev environment to control state. You
can later figure out where you want to put state based on your workflow. That
is, start with just the `terraform` binary and worry about Terraform Cloud
later.

**Resource.** In Terraform, every resource lines up with an actual vendor
resource. So, in AWS, you have a `resource` block for anything that might show
up in the AWS Console, incl. EC2 instance, each security group, EBS volumes,
EBS volume attachments, the 5+ things you have to do to get a load balancer,
... Some resources get inlined, e.g. EBS volume on a launch configuration, but
you are ultimately lining up one Terraform block per resource.

**One-at-a-time.** Even when you can inline a resource with a nested
declaration, prefer individual resources, e.g. `aws_security_group_rule`
instead of a block nested within `aws_instance`. doing so will prevent
lifecycle issues where Terraform has to destroy more than necessary in order to
guarantee synchronized configuration. This is particularly an issue when making
changes to data volumes, because you surely want to avoid destroying a volume
when adding additional block storage resources.

**Verbosity.** Terraform's one-to-one resource mapping gets very verbose, and
you'll find yourself wanting something higher level. Like a popular bingeworthy
show that you just don't get yet, stick with it. Remind yourself how hacky this
is in something like Ansible and how CloudFormation offers zero validation of
your configuration. You are better off laying out everything in a .tf file and
letting the non-clever, not-always-DRY configuration become your readable
infrastructure as code.

**Data.** Every `resource` block is also a `data` block; the former provisions,
the latter queries.

**Import.** It's a thing. You can `terraform import` existing resources to be
merged with your newly terraformed resources. Wait to do this until you have a
production-worthy setup, especially because you might want a newly terraformed
service anyway.

**Images.** You'll probably want Packer, even if you are not using
it. Otherwise, you'll find yourself stuffing a lot of configuration into AWS
user-data on EC2 instances. Your sensibilities might find user-data hacky, but
it's very reasonable after you spend some time with it. Note, however, that
user-data will slow down any auto-scaling group, so consider packing an AMI.

**Layout.** Terraform simply pulls in every .tf file. Outside of conventional
`output.tf` and `vars.tf` files, let yourself split up files however you see
fit. You don't need a `main.tf`. Avoid using `module` until you are certain
that you need it.

**Docs.** The Terraform docs are very good. These will become your best
resource, especially when you find blog posts lacking or outright missing.
Occasionally, you'll find that the examples in the docs leave out hugely
important details, like how an AWS load balancer needs to have public network
routed in order for a subnet in order for your load d-balancer to be accessible
from the public internet. This example is entirely undocumented when working
with more than a single VPC. When in doubt, consider how you'd do it in the AWS
Console manually, then line up resource by resource, one at a time.

**Usage.** Use Terraform 0.12 and later (just bump to 1.0 already!). Resist the
urge to use dynamic blocks, unless you are prepared to trade-off readability
for (non-intuitive) concision. Use `terraform fmt` with abandon. Be slow to use
`locals`. Use `random_string` when you need a random string, incl. service
passwords. Re-run `terraform init` whenever you add new "provider" blocks like
`locals` and `random`.

**Development.** Sign up for a new free-tier AWS account and develop in
"production." You'll probably only spend a few dollars over a few months (but
keep an eye on it instead of taking my word for it).
