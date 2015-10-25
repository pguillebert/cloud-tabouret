# cloud-tabouret

## Prerequisites

- ansible
- lein
- git
- boto

      sudo pip install boto

- a boto configuration in `~/.aws/credentials` with AWS credentials.
- A RSA keypair installed in the chosen region.

## OS information

For my tests I picked an AMI from
[Ubuntu cloud project](https://cloud-images.ubuntu.com/locator/ec2/).

I chose the following setup :

| Region     |   Ubuntu version  | arch  | Instance type |      AMI     | kernel |
|------------|-------------------|-------|---------------|--------------|--------|
| us-west-2  | vivid 15.04 DEVEL | amd64 |  hvm:ebs-ssd  | ami-6e67845d | hvm    |

Hence the ami to use is *ami-6e67845d*.

These AMI are regularly updated with the evolution of the distribution, so this AMI id
will be obsolete soon.

## Configuration file

The Ansible playbook pulls its parameters from the file `tabouret_config.yml`. The
following keys are used :


| Var name            | Description                      | Default value                |
|---------------------|----------------------------------|------------------------------|
| `app_name`          | Application name                 | "tabouret"                   |
| `app_repo`          | Github repository of the webapp  | "pguillebert/tabouret.git"   |
| `app_count`         | Number of instances to launch    | 1                            |
| `ec2_keypair`       | AWS keypair name to use          | "pguillebert"                |
| `ec2_instance_type` | EC2 instance to use              | "t2.micro"                   |
| `ec2_ami`           | AMI launched (cf OS information) | "ami-6e67845d"               |
| `ec2_region`        | AWS Region to use                | "us-west-2"                  |


## Deployment

Please source the ansible environment from the ansible directory:

      cd ANSIBLE_DIRECTORY
      source ./hacking/env-setup .

To skip validation of SSH unknown hosts:

      export ANSIBLE_HOST_KEY_CHECKING=False

To start the deployment of the application:

      ansible-playbook --private-key /Users/pguillebert/pguillebert deploy-all.yml

Here, the private key of the configured keypair will be used by ansible
to access the instances.

## Shortcomings

Obviously, all of this has a hackish smell to it. What could have been better with
more control on the setup, and more time :

- Use a central reference for built packages (for instance, a Jenkins that pushes
  jars into S3)
- Use internal networking instead of public IPs (needs VPN with VPC)
- Better generalization using templating to share configuration items
  with similar java services
- Scale instances up and down using ASGs or Ansible `count` advanced techniques
