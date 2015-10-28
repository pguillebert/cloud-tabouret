# cloud-tabouret

## Prerequisites

### ansible

Clone the latest devel branch from Github:

      git clone git://github.com/ansible/ansible.git -b devel --recursive ansible-bench

### leiningen

Refer to the documentation of tabouret to install leiningen.

### git

Whatever git version you have should be fine.

### boto and AWS account setup

First, install boto :

      sudo pip install boto

Please install a boto configuration in `~/.aws/credentials` with AWS credentials.

Also, you must have a RSA keypair installed in the chosen AWS region and be
in possession of the private key.

## OS information

For my tests I picked an AMI from
[Ubuntu cloud project](https://cloud-images.ubuntu.com/locator/ec2/).

I chose the following setup :

| Region     |   Ubuntu version  | arch  | Instance type |      AMI     | kernel |
|------------|-------------------|-------|---------------|--------------|--------|
| us-west-2  | vivid 15.04 DEVEL | amd64 |  hvm:ebs-ssd  | ami-6e67845d | hvm    |

Therefore the ami to use is *ami-6e67845d*.

These AMI are regularly updated with the evolution of the distribution, so this AMI id
will probably be obsolete soon. Please upgrade it accordingly if necessary.

## Configuration file

The Ansible playbook pulls all its parameters from the file `tabouret_config.yml`.
The following keys are used :

| Var name            | Description                      | Default value                |
|---------------------|----------------------------------|------------------------------|
| `app_name`          | Application name                 | "tabouret"                   |
| `app_repo`          | Github repository of the webapp  | "pguillebert/tabouret.git"   |
| `app_count`         | Number of instances to launch    | 2                            |
| `app_port`          | TCP port for the application     | 3003                         |
| `app_base_path`     | Base path of the HTTP REST client |                             |
| `app_version`       | Version of the JAR to use        | "0.1.0"                      |
| `ec2_keypair`       | AWS keypair name to use          | "pguillebert"                |
| `ec2_instance_type` | Type of EC2 instance to use      | "t2.micro"                   |
| `ec2_ami`           | AMI launched (cf OS information) | "ami-6e67845d"               |
| `ec2_region`        | AWS Region to use (here, Oregon) | "us-west-2"                  |


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

- Use a central reference for built packages (for instance, use a Jenkins that
  pushes compiled jars into S3)
- Use internal networking instead of public IPs (this would have needed a VPN
  connected to the VPC)
- Better generalization (more templating) to share configuration items
  with similar java services
- Scale instances up and down using ASGs or Ansible `count` advanced techniques
