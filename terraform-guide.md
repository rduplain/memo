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
