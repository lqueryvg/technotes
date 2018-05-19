# Terraform

## init
Download zip & unzip into PATH somewhere.

    terraform init      # in work directory

    mkdir  ~/terraform-plugins      # specify an alternate plugin path
                                    # manually copy plugins to here

    terraform init -plugin-dir ~/terraform-plugins
        # updates .terraform/plugin_path to point to persistent, shared dir for plugins

## plan
    -/+ means destroy/create
    ~ means in place update

## apply
## plan -destroy
## destroy
## graph


## Misc
    ${aws_instance.example.id}   # get id from aws_instance.example resource

    depends_on = ["aws_instance.example"]  # explicit dependency

## Provisioners

    provisioner "local-exec" {
        command = "echo ${aws_instance.example.public_ip} > ip_address.txt"
    }
    # runs on machine but ONLY when resource is created

Recommend using normal shutdown scripts where possible for destroy.

## Variables

Terraform loads all files ending in `.tf` in a directory.
Create a file named `terraform.tfvars` with the following contents:

    access_key = "foo"
    secret_key = "bar"

Accessing variables:

    variable "access_key" {}
    variable "secret_key" {}
    variable "region" {
        default = "us-east-1"
    }

    provider "aws" {
        access_key = "${var.access_key}"
        secret_key = "${var.secret_key}"
        region     = "${var.region}"
    }

Other ways to pass variables:

    $ terraform plan \
        -var 'access_key=foo' \
        -var 'secret_key=bar'

    $ terraform plan \
        -var-file="secret.tfvars" \
        -var-file="production.tfvars"

## Output

    output "ip" {
        value = "${aws_eip.ip.public_ip}"
    }

    $ terraform output ip

## Console

    terraform console
    > azurerm_resource_group.jb-test.location

# Import

    terraform import {terraform-provider}.{terraform-name} {real-name}

E.g.:

    terraform import aws_s3_bucket.{terraform-name} {aws-name}
    terraform import azurerm_resource_group.jb-test /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/jb-test

# Debugging

    export TF_LOG=TRACE
