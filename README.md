<!-- 














  ** DO NOT EDIT THIS FILE
  ** 
  ** This file was automatically generated by the `build-harness`. 
  ** 1) Make all changes to `README.yaml` 
  ** 2) Run `make init` (you only need to do this once)
  ** 3) Run`make readme` to rebuild this file. 
  **
  ** (We maintain HUNDREDS of open source projects. This is how we maintain our sanity.)
  **















  -->
[![README Header][readme_header_img]][readme_header_link]

[![Cloud Posse][logo]](https://cpco.io/homepage)

# terraform-aws-ecs-alb-service-task [![Codefresh Build Status](https://g.codefresh.io/api/badges/pipeline/cloudposse/terraform-modules%2Fterraform-aws-ecs-alb-service-task?type=cf-1)](https://g.codefresh.io/public/accounts/cloudposse/pipelines/5db352c10c7c5a56af1de612) [![Latest Release](https://img.shields.io/github/release/cloudposse/terraform-aws-ecs-alb-service-task.svg)](https://github.com/cloudposse/terraform-aws-ecs-alb-service-task/releases/latest) [![Slack Community](https://slack.cloudposse.com/badge.svg)](https://slack.cloudposse.com)


Terraform module to create an ECS Service for a web app (task), and an ALB target group to route requests.


---

This project is part of our comprehensive ["SweetOps"](https://cpco.io/sweetops) approach towards DevOps. 
[<img align="right" title="Share via Email" src="https://docs.cloudposse.com/images/ionicons/ios-email-outline-2.0.1-16x16-999999.svg"/>][share_email]
[<img align="right" title="Share on Google+" src="https://docs.cloudposse.com/images/ionicons/social-googleplus-outline-2.0.1-16x16-999999.svg" />][share_googleplus]
[<img align="right" title="Share on Facebook" src="https://docs.cloudposse.com/images/ionicons/social-facebook-outline-2.0.1-16x16-999999.svg" />][share_facebook]
[<img align="right" title="Share on Reddit" src="https://docs.cloudposse.com/images/ionicons/social-reddit-outline-2.0.1-16x16-999999.svg" />][share_reddit]
[<img align="right" title="Share on LinkedIn" src="https://docs.cloudposse.com/images/ionicons/social-linkedin-outline-2.0.1-16x16-999999.svg" />][share_linkedin]
[<img align="right" title="Share on Twitter" src="https://docs.cloudposse.com/images/ionicons/social-twitter-outline-2.0.1-16x16-999999.svg" />][share_twitter]


[![Terraform Open Source Modules](https://docs.cloudposse.com/images/terraform-open-source-modules.svg)][terraform_modules]



It's 100% Open Source and licensed under the [APACHE2](LICENSE).







We literally have [*hundreds of terraform modules*][terraform_modules] that are Open Source and well-maintained. Check them out! 







## Usage


**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/cloudposse/terraform-aws-ecs-alb-service-task/releases).



For a complete example, see [examples/complete](examples/complete).

For automated test of the complete example using `bats` and `Terratest`, see [test](test).

```hcl
  provider "aws" {
    region = var.region
  }

  module "label" {
    source     = "git::https://github.com/cloudposse/terraform-null-label.git?ref=tags/0.15.0"
    namespace  = var.namespace
    name       = var.name
    stage      = var.stage
    delimiter  = var.delimiter
    attributes = var.attributes
    tags       = var.tags
  }

  module "vpc" {
    source     = "git::https://github.com/cloudposse/terraform-aws-vpc.git?ref=tags/0.8.1"
    namespace  = var.namespace
    stage      = var.stage
    name       = var.name
    delimiter  = var.delimiter
    attributes = var.attributes
    cidr_block = var.vpc_cidr_block
    tags       = var.tags
  }

  module "subnets" {
    source               = "git::https://github.com/cloudposse/terraform-aws-dynamic-subnets.git?ref=tags/0.16.1"
    availability_zones   = var.availability_zones
    namespace            = var.namespace
    stage                = var.stage
    name                 = var.name
    attributes           = var.attributes
    delimiter            = var.delimiter
    vpc_id               = module.vpc.vpc_id
    igw_id               = module.vpc.igw_id
    cidr_block           = module.vpc.vpc_cidr_block
    nat_gateway_enabled  = true
    nat_instance_enabled = false
    tags                 = var.tags
  }

  resource "aws_ecs_cluster" "default" {
    name = module.label.id
    tags = module.label.tags
  }

  module "container_definition" {
    source                       = "git::https://github.com/cloudposse/terraform-aws-ecs-container-definition.git?ref=tags/0.21.0"
    container_name               = var.container_name
    container_image              = var.container_image
    container_memory             = var.container_memory
    container_memory_reservation = var.container_memory_reservation
    container_cpu                = var.container_cpu
    essential                    = var.container_essential
    readonly_root_filesystem     = var.container_readonly_root_filesystem
    environment                  = var.container_environment
    port_mappings                = var.container_port_mappings
    log_configuration            = var.container_log_configuration
  }

  module "ecs_alb_service_task" {
    source                             = "git::https://github.com/cloudposse/terraform-aws-ecs-alb-service-task.git?ref=master"
    namespace                          = var.namespace
    stage                              = var.stage
    name                               = var.name
    attributes                         = var.attributes
    delimiter                          = var.delimiter
    alb_security_group                 = module.vpc.vpc_default_security_group_id
    container_definition_json          = module.container_definition.json
    ecs_cluster_arn                    = aws_ecs_cluster.default.arn
    launch_type                        = var.ecs_launch_type
    vpc_id                             = module.vpc.vpc_id
    security_group_ids                 = [module.vpc.vpc_default_security_group_id]
    subnet_ids                         = module.subnets.public_subnet_ids
    tags                               = var.tags
    ignore_changes_task_definition     = var.ignore_changes_task_definition
    network_mode                       = var.network_mode
    assign_public_ip                   = var.assign_public_ip
    propagate_tags                     = var.propagate_tags
    health_check_grace_period_seconds  = var.health_check_grace_period_seconds
    deployment_minimum_healthy_percent = var.deployment_minimum_healthy_percent
    deployment_maximum_percent         = var.deployment_maximum_percent
    deployment_controller_type         = var.deployment_controller_type
    desired_count                      = var.desired_count
    task_memory                        = var.task_memory
    task_cpu                           = var.task_cpu
  }
```

The `container_image` in the `container_definition` module is the Docker image used to start a container.

This string is passed directly to the Docker daemon. Images in the Docker Hub registry are available by default.
Other repositories are specified with either `repository-url/image:tag` or `repository-url/image@digest`.
Up to 255 letters (uppercase and lowercase), numbers, hyphens, underscores, colons, periods, forward slashes, and number signs are allowed.
This parameter maps to Image in the Create a container section of the Docker Remote API and the IMAGE parameter of `docker run`.

When a new task starts, the Amazon ECS container agent pulls the latest version of the specified image and tag for the container to use.
However, subsequent updates to a repository image are not propagated to already running tasks.

Images in Amazon ECR repositories can be specified by either using the full `registry/repository:tag` or `registry/repository@digest`.
For example, `012345678910.dkr.ecr.<region-name>.amazonaws.com/<repository-name>:latest` or `012345678910.dkr.ecr.<region-name>.amazonaws.com/<repository-name>@sha256:94afd1f2e64d908bc90dbca0035a5b567EXAMPLE`.

Images in official repositories on Docker Hub use a single name (for example, `ubuntu` or `mongo`).

Images in other repositories on Docker Hub are qualified with an organization name (for example, `amazon/amazon-ecs-agent`).

Images in other online repositories are qualified further by a domain name (for example, `quay.io/assemblyline/ubuntu`).

For more info, see [Container Definition](https://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_ContainerDefinition.html).






## Makefile Targets
```
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen

```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| alb_security_group | Security group of the ALB | string | `` | no |
| assign_public_ip | Assign a public IP address to the ENI (Fargate launch type only). Valid values are `true` or `false`. Default `false` | bool | `false` | no |
| attributes | Additional attributes (_e.g._ "1") | list(string) | `<list>` | no |
| capacity_provider_strategies | The capacity provider strategies to use for the service. See `capacity_provider_strategy` configuration block: https://www.terraform.io/docs/providers/aws/r/ecs_service.html#capacity_provider_strategy | object | `<list>` | no |
| container_definition_json | The JSON of the task container definition | string | - | yes |
| container_port | The port on the container to allow via the ingress security group | number | `80` | no |
| delimiter | Delimiter between `namespace`, `stage`, `name` and `attributes` | string | `-` | no |
| deployment_controller_type | Type of deployment controller. Valid values are `CODE_DEPLOY` and `ECS` | string | `ECS` | no |
| deployment_maximum_percent | The upper limit of the number of tasks (as a percentage of `desired_count`) that can be running in a service during a deployment | number | `200` | no |
| deployment_minimum_healthy_percent | The lower limit (as a percentage of `desired_count`) of the number of tasks that must remain running and healthy in a service during a deployment | number | `100` | no |
| desired_count | The number of instances of the task definition to place and keep running | number | `1` | no |
| ecs_cluster_arn | The ARN of the ECS cluster where service will be provisioned | string | - | yes |
| ecs_load_balancers | A list of load balancer config objects for the ECS service; see `load_balancer` docs https://www.terraform.io/docs/providers/aws/r/ecs_service.html | object | `<list>` | no |
| enable_ecs_managed_tags | Specifies whether to enable Amazon ECS managed tags for the tasks within the service | bool | `false` | no |
| enabled | Set to false to prevent the module from creating any resources | bool | `true` | no |
| health_check_grace_period_seconds | Seconds to ignore failing load balancer health checks on newly instantiated tasks to prevent premature shutdown, up to 7200. Only valid for services configured to use load balancers | number | `0` | no |
| ignore_changes_task_definition | Whether to ignore changes in container definition and task definition in the ECS service | bool | `true` | no |
| launch_type | The launch type on which to run your service. Valid values are `EC2` and `FARGATE` | string | `FARGATE` | no |
| name | Name of the application | string | - | yes |
| namespace | Namespace (e.g. `eg` or `cp`) | string | `` | no |
| network_mode | The network mode to use for the task. This is required to be `awsvpc` for `FARGATE` `launch_type` | string | `awsvpc` | no |
| nlb_cidr_blocks | A list of CIDR blocks to add to the ingress rule for the NLB container port | list(string) | `<list>` | no |
| nlb_container_port | The port on the container to allow via the ingress security group | number | `80` | no |
| ordered_placement_strategy | Service level strategy rules that are taken into consideration during task placement. List from top to bottom in order of precedence. The maximum number of ordered_placement_strategy blocks is 5. See `ordered_placement_strategy` docs https://www.terraform.io/docs/providers/aws/r/ecs_service.html#ordered_placement_strategy-1 | object | `<list>` | no |
| permissions_boundary | A permissions boundary ARN to apply to the 3 roles that are created. | string | `` | no |
| platform_version | The platform version on which to run your service. Only applicable for launch_type set to FARGATE. More information about Fargate platform versions can be found in the AWS ECS User Guide. | string | `LATEST` | no |
| propagate_tags | Specifies whether to propagate the tags from the task definition or the service to the tasks. The valid values are SERVICE and TASK_DEFINITION | string | `null` | no |
| proxy_configuration | The proxy configuration details for the App Mesh proxy. See `proxy_configuration` docs https://www.terraform.io/docs/providers/aws/r/ecs_task_definition.html#proxy-configuration-arguments | object | `null` | no |
| scheduling_strategy | The scheduling strategy to use for the service. The valid values are REPLICA and DAEMON. Note that Fargate tasks do not support the DAEMON scheduling strategy. | string | `REPLICA` | no |
| security_group_ids | Security group IDs to allow in Service `network_configuration` | list(string) | `<list>` | no |
| service_placement_constraints | The rules that are taken into consideration during task placement. Maximum number of placement_constraints is 10. See `placement_constraints` docs https://www.terraform.io/docs/providers/aws/r/ecs_service.html#placement_constraints-1 | object | `<list>` | no |
| service_registries | The service discovery registries for the service. The maximum number of service_registries blocks is 1. The currently supported service registry is Amazon Route 53 Auto Naming Service - `aws_service_discovery_service`; see `service_registries` docs https://www.terraform.io/docs/providers/aws/r/ecs_service.html#service_registries-1 | object | `<list>` | no |
| stage | Stage (e.g. `prod`, `dev`, `staging`) | string | `` | no |
| subnet_ids | Subnet IDs | list(string) | - | yes |
| tags | Additional tags (_e.g._ { BusinessUnit : ABC }) | map(string) | `<map>` | no |
| task_cpu | The number of CPU units used by the task. If using `FARGATE` launch type `task_cpu` must match supported memory values (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#task_size) | number | `256` | no |
| task_memory | The amount of memory (in MiB) used by the task. If using Fargate launch type `task_memory` must match supported cpu value (https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definition_parameters.html#task_size) | number | `512` | no |
| task_placement_constraints | A set of placement constraints rules that are taken into consideration during task placement. Maximum number of placement_constraints is 10. See `placement_constraints` docs https://www.terraform.io/docs/providers/aws/r/ecs_task_definition.html#placement-constraints-arguments | object | `<list>` | no |
| use_alb_security_group | A flag to enable/disable adding the ingress rule to the ALB security group | bool | `false` | no |
| use_nlb_cidr_blocks | A flag to enable/disable adding the NLB ingress rule to the security group | bool | `false` | no |
| volumes | Task volume definitions as list of configuration objects | object | `<list>` | no |
| vpc_id | The VPC ID where resources are created | string | - | yes |

## Outputs

| Name | Description |
|------|-------------|
| ecs_exec_role_policy_id | The ECS service role policy ID, in the form of `role_name:role_policy_name` |
| ecs_exec_role_policy_name | ECS service role name |
| service_name | ECS Service name |
| service_role_arn | ECS Service role ARN |
| service_security_group_id | Security Group ID of the ECS task |
| task_definition_family | ECS task definition family |
| task_definition_revision | ECS task definition revision |
| task_exec_role_arn | ECS Task exec role ARN |
| task_exec_role_name | ECS Task role name |
| task_role_arn | ECS Task role ARN |
| task_role_id | ECS Task role id |
| task_role_name | ECS Task role name |




## Share the Love 

Like this project? Please give it a ★ on [our GitHub](https://github.com/cloudposse/terraform-aws-ecs-alb-service-task)! (it helps us **a lot**) 

Are you using this project or any of our other projects? Consider [leaving a testimonial][testimonial]. =)


## Related Projects

Check out these related projects.

- [terraform-aws-alb](https://github.com/cloudposse/terraform-aws-alb) - Terraform module to provision a standard ALB for HTTP/HTTP traffic
- [terraform-aws-alb-ingress](https://github.com/cloudposse/terraform-aws-alb-ingress) - Terraform module to provision an HTTP style ingress rule based on hostname and path for an ALB
- [terraform-aws-codebuild](https://github.com/cloudposse/terraform-aws-codebuild) - Terraform Module to easily leverage AWS CodeBuild for Continuous Integration
- [terraform-aws-ecr](https://github.com/cloudposse/terraform-aws-ecr) - Terraform Module to manage Docker Container Registries on AWS ECR
- [terraform-aws-ecs-web-app](https://github.com/cloudposse/terraform-aws-ecs-web-app) - Terraform module that implements a web app on ECS and supporting AWS resources
- [terraform-aws-ecs-codepipeline](https://github.com/cloudposse/terraform-aws-ecs-codepipeline) - Terraform Module for CI/CD with AWS Code Pipeline and Code Build for ECS
- [terraform-aws-ecs-cloudwatch-sns-alarms](https://github.com/cloudposse/terraform-aws-ecs-cloudwatch-sns-alarms) - Terraform module to create CloudWatch Alarms on ECS Service level metrics
- [terraform-aws-ecs-container-definition](https://github.com/cloudposse/terraform-aws-ecs-container-definition) - Terraform module to generate well-formed JSON documents that are passed to the aws_ecs_task_definition Terraform resource
- [terraform-aws-lb-s3-bucket](https://github.com/cloudposse/terraform-aws-lb-s3-bucket) - Terraform module to provision an S3 bucket with built in IAM policy to allow AWS Load Balancers to ship access logs.



## Help

**Got a question?** We got answers. 

File a GitHub [issue](https://github.com/cloudposse/terraform-aws-ecs-alb-service-task/issues), send us an [email][email] or join our [Slack Community][slack].

[![README Commercial Support][readme_commercial_support_img]][readme_commercial_support_link]

## DevOps Accelerator for Startups


We are a [**DevOps Accelerator**][commercial_support]. We'll help you build your cloud infrastructure from the ground up so you can own it. Then we'll show you how to operate it and stick around for as long as you need us. 

[![Learn More](https://img.shields.io/badge/learn%20more-success.svg?style=for-the-badge)][commercial_support]

Work directly with our team of DevOps experts via email, slack, and video conferencing.

We deliver 10x the value for a fraction of the cost of a full-time engineer. Our track record is not even funny. If you want things done right and you need it done FAST, then we're your best bet.

- **Reference Architecture.** You'll get everything you need from the ground up built using 100% infrastructure as code.
- **Release Engineering.** You'll have end-to-end CI/CD with unlimited staging environments.
- **Site Reliability Engineering.** You'll have total visibility into your apps and microservices.
- **Security Baseline.** You'll have built-in governance with accountability and audit logs for all changes.
- **GitOps.** You'll be able to operate your infrastructure via Pull Requests.
- **Training.** You'll receive hands-on training so your team can operate what we build.
- **Questions.** You'll have a direct line of communication between our teams via a Shared Slack channel.
- **Troubleshooting.** You'll get help to triage when things aren't working.
- **Code Reviews.** You'll receive constructive feedback on Pull Requests.
- **Bug Fixes.** We'll rapidly work with you to fix any bugs in our projects.

## Slack Community

Join our [Open Source Community][slack] on Slack. It's **FREE** for everyone! Our "SweetOps" community is where you get to talk with others who share a similar vision for how to rollout and manage infrastructure. This is the best place to talk shop, ask questions, solicit feedback, and work together as a community to build totally *sweet* infrastructure.

## Discourse Forums

Participate in our [Discourse Forums][discourse]. Here you'll find answers to commonly asked questions. Most questions will be related to the enormous number of projects we support on our GitHub. Come here to collaborate on answers, find solutions, and get ideas about the products and services we value. It only takes a minute to get started! Just sign in with SSO using your GitHub account.

## Newsletter

Sign up for [our newsletter][newsletter] that covers everything on our technology radar.  Receive updates on what we're up to on GitHub as well as awesome new projects we discover. 

## Office Hours

[Join us every Wednesday via Zoom][office_hours] for our weekly "Lunch & Learn" sessions. It's **FREE** for everyone! 

[![zoom](https://img.cloudposse.com/fit-in/200x200/https://cloudposse.com/wp-content/uploads/2019/08/Powered-by-Zoom.png")][office_hours]

## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/cloudposse/terraform-aws-ecs-alb-service-task/issues) to report any bugs or file feature requests.

### Developing

If you are interested in being a contributor and want to get involved in developing this project or [help out](https://cpco.io/help-out) with our other projects, we would love to hear from you! Shoot us an [email][email].

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2020 [Cloud Posse, LLC](https://cpco.io/copyright)



## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.









## Trademarks

All other trademarks referenced herein are the property of their respective owners.

## About

This project is maintained and funded by [Cloud Posse, LLC][website]. Like it? Please let us know by [leaving a testimonial][testimonial]!

[![Cloud Posse][logo]][website]

We're a [DevOps Professional Services][hire] company based in Los Angeles, CA. We ❤️  [Open Source Software][we_love_open_source].

We offer [paid support][commercial_support] on all of our projects.  

Check out [our other projects][github], [follow us on twitter][twitter], [apply for a job][jobs], or [hire us][hire] to help with your cloud strategy and implementation.



### Contributors

|  [![Erik Osterman][osterman_avatar]][osterman_homepage]<br/>[Erik Osterman][osterman_homepage] | [![Igor Rodionov][goruha_avatar]][goruha_homepage]<br/>[Igor Rodionov][goruha_homepage] | [![Andriy Knysh][aknysh_avatar]][aknysh_homepage]<br/>[Andriy Knysh][aknysh_homepage] | [![Sarkis Varozian][sarkis_avatar]][sarkis_homepage]<br/>[Sarkis Varozian][sarkis_homepage] |
|---|---|---|---|

  [osterman_homepage]: https://github.com/osterman
  [osterman_avatar]: https://img.cloudposse.com/150x150/https://github.com/osterman.png
  [goruha_homepage]: https://github.com/goruha
  [goruha_avatar]: https://img.cloudposse.com/150x150/https://github.com/goruha.png
  [aknysh_homepage]: https://github.com/aknysh
  [aknysh_avatar]: https://img.cloudposse.com/150x150/https://github.com/aknysh.png
  [sarkis_homepage]: https://github.com/sarkis
  [sarkis_avatar]: https://img.cloudposse.com/150x150/https://github.com/sarkis.png

[![README Footer][readme_footer_img]][readme_footer_link]
[![Beacon][beacon]][website]

  [logo]: https://cloudposse.com/logo-300x69.svg
  [docs]: https://cpco.io/docs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=docs
  [website]: https://cpco.io/homepage?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=website
  [github]: https://cpco.io/github?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=github
  [jobs]: https://cpco.io/jobs?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=jobs
  [hire]: https://cpco.io/hire?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=hire
  [slack]: https://cpco.io/slack?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=slack
  [linkedin]: https://cpco.io/linkedin?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=linkedin
  [twitter]: https://cpco.io/twitter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=twitter
  [testimonial]: https://cpco.io/leave-testimonial?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=testimonial
  [office_hours]: https://cloudposse.com/office-hours?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=office_hours
  [newsletter]: https://cpco.io/newsletter?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=newsletter
  [discourse]: https://ask.sweetops.com/?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=discourse
  [email]: https://cpco.io/email?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=email
  [commercial_support]: https://cpco.io/commercial-support?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=commercial_support
  [we_love_open_source]: https://cpco.io/we-love-open-source?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=we_love_open_source
  [terraform_modules]: https://cpco.io/terraform-modules?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=terraform_modules
  [readme_header_img]: https://cloudposse.com/readme/header/img
  [readme_header_link]: https://cloudposse.com/readme/header/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=readme_header_link
  [readme_footer_img]: https://cloudposse.com/readme/footer/img
  [readme_footer_link]: https://cloudposse.com/readme/footer/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=readme_footer_link
  [readme_commercial_support_img]: https://cloudposse.com/readme/commercial-support/img
  [readme_commercial_support_link]: https://cloudposse.com/readme/commercial-support/link?utm_source=github&utm_medium=readme&utm_campaign=cloudposse/terraform-aws-ecs-alb-service-task&utm_content=readme_commercial_support_link
  [share_twitter]: https://twitter.com/intent/tweet/?text=terraform-aws-ecs-alb-service-task&url=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [share_linkedin]: https://www.linkedin.com/shareArticle?mini=true&title=terraform-aws-ecs-alb-service-task&url=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [share_reddit]: https://reddit.com/submit/?url=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [share_facebook]: https://facebook.com/sharer/sharer.php?u=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [share_googleplus]: https://plus.google.com/share?url=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [share_email]: mailto:?subject=terraform-aws-ecs-alb-service-task&body=https://github.com/cloudposse/terraform-aws-ecs-alb-service-task
  [beacon]: https://ga-beacon.cloudposse.com/UA-76589703-4/cloudposse/terraform-aws-ecs-alb-service-task?pixel&cs=github&cm=readme&an=terraform-aws-ecs-alb-service-task
