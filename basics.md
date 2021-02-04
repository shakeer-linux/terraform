```

Terraform Basics



Terraform Commands:

https://www.terraform.io/docs/commands/index.html

terraform fmt									: 	Terraform fmt command automatically updates configurations in the current directory for easy readability and consistency.
terraform validate								:	Validates the configuration files in a dir
terraform init									:	To initialize the provider
terraform plan								:		It is used to create an execution plan. It check or dry run without making any changes to real resources
terraform apply							       	:	Scans the current directory for the configuration and applies the changes appropriately
terraform refresh								:	To refresh the current state
terraform show								:	To see the contents of your tfstate files
terraform state list							:	If you have a long state file, we may want a list of the resources in state. It will list.
terraform destroy -target aws_instance.myec2		:	Destroying specific resource
terraform taint aws_instance.terrafom-instance	:	it will recreate there resource.

Certification:

https://www.hashicorp.com/certification/terraform-associate

Review Certification before:
https://learn.hashicorp.com/collections/terraform/certification
 

About:
Terraform is a infrastructure orchestration tool, infrastructure as a code(IAC) platform. 
We have various type of tools in that can allow you to deploy infrastructure as code.
Terraform, Ansible, Cloud Formation, Heat, SaltStack, Chef, Puppet and others
Ansible as configuration tool but, we can also use as IaC.
Ansible, Puppet other configuration tools primarily designed for install and manage the software on existing servers.
Terraform, Cloud formation are primarily allow us to provision the servers and service, others only by themselves.
Configuration tools are can do some of infrastructure provisioning work. But they are better fit for configuration management.
We can also integrate configuration management tools with terraform, like ansible
Terraform support for multiple platforms, has no of providers.
Cloud formation is only used for AWS.
Terraform can also used for AWS Cloud, other providers. It can support on hybrid cloud.


Provider:
Terraform supports multiple providers.
We have to add specific provider for which and where we want launch a infrastructure. If for Aws we have to use aws provider in terraform.
We need and add the authentication tokens or credentials in terraform to provision infrastructre.
After adding token we have to initialize the terraform. The initialization of terraform will downloaded the plugins associated with the provider.
Eg: For AWS : we need AWS Access and Secret for Digital ocean: we need Tokens.
Resources are referred as service in terraform. Like aws_instance, iam_user, 


State:
Desired State, Current State. 
Terraform refresh and terraform show will show the current state of the infrastructure.
If we do terraform apply will take you the to the desired state of infrastructure if any changes occurred in previously.
And, the desired state of infrastructure will applied on what we defined resources in terraform file. 
Terraform apply will not take you to the current state as desired state on other changes on resources were not defined in terraform file.

Provider:
There are two major categories for terraform providers.
HashiCorp Distributed  
3rd Party Providers     (https://www.terraform.io/docs/providers/type/community-index.html).
HashiCorp distributed providers - are officially tested and can be downloaded during terraform initializing.
3rd Party providers - are built by the community of terraform users and vendors. Not tested by HashiCorp. 
Third Party Providers must be installed manually, since terraform init cannot automatically download them.
Third Party providers installed by placing their plugin executables in user plugin directory. i.e.; ~/.terraform.d/plugins 

Attributes and Output files:
Terraform has capability of fetch attributes value of resources. Like example you want get output of public ip of ec2 instance then you can get it.
We can also use this attribute as input to other resources.
To check the attributes supported for providers. We need to go official page of provider and check for attribute reference. Eg: https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/eip#attributes-reference
The out of value can also be stored in terraform.tstate file.
We can use one of resource value attribute output to create other resource. Can be used as input to other resource.

Variables:
In Terraform we can declare variable in different ways.
1. Using default variable in file. 2..in file declaration.  3. command line cli,    4. environment variables
variables.tf is the file where we can set values for variables. These values are default.
terraform.tfvars is the file where we can assign the values to variable. If we don’t specify then for the variable the value will be assigned from variables.tf file
We can explicitly define a variable file in CLI. If you don’t have a terraform.tfvars file. Need to specify in cli like —> terraform plan -var-file=“custom.tfvars”.
Using environment variable: export TF_VAR_<varialbename> eg: TF_VAR_instancetype=“t2.micro”   - we specify.
In
In Variable section : if you mention the variable values in terraform.tfvars file it will take that value. If you don’t specify then it will take the default value from variables.tf file.
If you still not provided default value then it will ask the value for variable when you do terraform apply or terraform plan
Maps, list
We can add values like key pair value in variable in terraform using map type. 
We need to specify the variable type in variable.tf file like “type=map” and assign the value in eg: variable “map1” {  type = map \nextline default = { key1 : “value1” \nextline key2 : “value2”} \next line } }
Then we do fetch the value by mentioning : var.map1[key1]
We can also add value like in list to variable in terraform using list type
We need to specify the variable type in variable.tf file like “type=list” and assign the value in eg:  variable “list1” {  type = list \next line default = { “value1”, “value2”} \nextline}
Then we do fetch the value using index value in our main file.tf : var.list1[0] or var.list1[1]

Count & count.index:
Count parameter is used create same resource multiple time with simple configuration.
Like :  resource “aws_instance” “some_name” \nl { \nl ami = someid \nl instance_type = sometype \nl count=5 \nl} 
The above configuration will create 5 instances
Count Index is used as loop or iteration if you want to make different names for resources. Some time same name will not applied like for create multiple user with same name can’t be created so in that case we will use the count.index
resource “aws_iam_user” “lb”{
   name = “loadbalancer.${count.index}”
   count = 3
   Path = “/system/"
}
So the above, config will create loadbalancer1, loadbalancer2, loadbalancer3 users. We can also use, the function with list to create resource  with specified values name in list values.

Conditional Expressions:
A conditional expression uses the value for a bool expression to select one of two values.
condition ? true_val : false_value

Local Values:
Local values can be helpful to avoid repeating the same values or expressions multiple times in a configuration
For eg: in AWS we want add some tag to multiple resources. So, then we add that tags under one value the we using the value we attache it to tags variable in terraform.
locals {
  common_tags = {
    Owner = "DevOps Team"
    service = "backend"
  }
}
resource "aws_instance" "app-dev" {
   ami = "ami-082b5a644766e0e6f"
   instance_type = "t2.micro"
   tags = local.common_tags
}

resource "aws_instance" "db-dev" {
   ami = "ami-082b5a644766e0e6f"
   instance_type = "t2.small"
   tags = local.common_tags
}
If above eg we defined common_tags and under locals. And assigning in multiple resource blocks. With this we can avoid length of the code, add duplication, and time consuming.

Functions:
Terraform provides number of built-in functions. which are catergerized in different types. We have multiple function we can use as per our requirement. 
The Terraform language does not support user-defined functions, and so only the functions built in to the language are available for use.
https://www.terraform.io/docs/configuration/functions.html
We can experiment with the behavior of Terraform's built-in functions from the Terraform expression console, by running the terraform console command
$ terraform console
> max(11,20,19)
20
>
Another eg:
$ cat file.txt
Hi! This Shakeer Pasha Mohammed
$ terraform console
> file("file.txt")
Hi! This Shakeer Pasha Mohammed
>

Another eg: in Below Example, we are using file, timestamp, formatdate built in functions.
With file: we are create a instance using a existing key. 
With timestamp : we are outputting a current time with timestamp also formatting in our required format.
locals {
  time = formatdate("DD MMM YYYY hh:mm ZZZ", timestamp())
}
resource "aws_key_pair" "loginkey" {
  key_name   = "login-key"
  public_key = file("id_rsa.pub")
}
resource "aws_instance" "app-dev" {
   ami = ami
   instance_type = "t2.micro"
   key_name = aws_key_pair.loginkey.key_name
   }
}

output "timestamp" {
  value = local.time
}
######

locals {
   time1 = formatdate(("EEEE, DD-MMM-YY hh:mm:ss ZZZ"), timestamp())
}

output "present-time" {
   value = local.time1
}
—— terraform apply
Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

present-time = Monday, 02-Nov-20 15:36:59 UTC
PRINHYLTPAP1074:aws-terraform shakeerp$
Data Source:
Data Source is like it will fetch the data for use elsewhere in Terraform configuration.
For example : you need a ami id information for aws instance. It will change according to region. You want to fetch the ami id what ever the region. You can get ami id information using data sources.
The below eg will fetch the ami id as per region in provider. You don’t need to update manually ami id.

data "aws_ami" "app_ami" {
  most_recent = true
  owners = ["amazon"]


  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}

resource "aws_instance" "instance-1" {
    ami = data.aws_ami.app_ami.id
   instance_type = "t2.micro"
}

Terraform Debugging:

Terraform has ability show the details logs like verbose data of what it is doing in the backed of terraform commands also other resource logs.
So, for that we need to enable the environment variable in terminal
export TF_LOG = TRACE  — > it will show you most verbose details.
Other are :  TRACE, WARN, INFO, DEBUG, ERROR are supported fields for the TF_LOG env variable.
Eg:
$ export TF_LOG=TRACE
PRINHYLTPAP1074:aws-terraform shakeerp$ terraform plan
2020/11/03 14:56:12 [INFO] Terraform version: 0.13.5
2020/11/03 14:56:12 [INFO] Go runtime version: go1.14.7
2020/11/03 14:56:12 [INFO] CLI args: []string{"/usr/local/bin/terraform", "plan"}
2020/11/03 14:56:12 [DEBUG] Attempting to open CLI config file: /Users/shakeerp/.terraformrc
2020/11/03 14:56:12 [DEBUG] File doesn't exist, but doesn't need to. Ignoring.

Else if you want to put all the debug or log information into a file rather than showing lengthy output in terminal.
export TF_LOG_PATH = /tmp/terraform.txt

Terraform format
Terraform format will be used for arraigning the terraform configuration in a terraform format.
terraform fmt : this command will re-arrange the configuration in required terraform format. It will re-write the code.

Terraform Validate:
Terraform validate is used to checks the configuration whether is valid or not.
It will also check unsupported arguments, un declared variables and others.
terraform validate : this command is used to validate.

Terraform taint:
Terraform taint is used recreate a resource. Instead of doing terraform destroy and terraform apply, just apply taint. It will recreate.
For example you have terraform configuration for multiple resources, in that you want recreate a resource. Just taint that resource. And apply then it will re-create that resource.
This will be applied on only on that resource. Will not re-create other resources.
terraform taint resource.<resource_name>
This command will not modify infrastructure, but does modify the state file in order to mark a resource as tainted. Once a resource is marked as tainted, the next plan will show that the resource will be destroyed and recreated and the next apply will implement this change.

$ terraform state list
aws_instance.terrafom-instance
$ terraform taint aws_instance.terrafom-instance
Resource instance aws_instance.terrafom-instance has been marked as tainted.
$


Terraform Refresh  


Terraform Plan Out


Terraform Output:


Terraform Graph

It is used to generate a visual representation of either a configuration or execution plan. The output is in the DOT format, which can be used by GraphViz to generate charts.
Terraform uses a graph to keep track of infrastructure dependencies.
Terraform builds a graph from the Terraform configurations or execution plan.
Created 4 resources

aws_instance.inst2: Creating...
aws_instance.inst1: Creating...
aws_instance.inst2: Still creating... [10s elapsed]
aws_instance.inst1: Still creating... [10s elapsed]
aws_instance.inst1: Still creating... [20s elapsed]
aws_instance.inst2: Still creating... [20s elapsed]
aws_instance.inst2: Creation complete after 30s [id=i-0afcddbe68e5a28cb]
aws_instance.inst1: Still creating... [30s elapsed]
aws_instance.inst1: Creation complete after 37s [id=i-0d506b1e881dc1064]
aws_eip.lb: Creating...
aws_eip.lb: Creation complete after 5s [id=eipalloc-0e63d81ac0d084f2f]
aws_security_group.allow_tls: Creating...
aws_security_group.allow_tls: Still creating... [10s elapsed]
aws_security_group.allow_tls: Creation complete after 11s [id=sg-0ca17ac41b378f224]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
Created a DOT file of  resources.

$ terraform graph > graph.dot

	Install the graphviz.  This is used convert the dot file into svg format.

# sudo apt install graphviz
# cat graph.dot | dot -Tsvg > graph.svg
# ls
graph.dot  graph.svg
#
Open this file graph.svg with browser you will get visualize the all resource in graph.

file:///Users/shakeerp/Desktop/Terraform/aws-terraform/graph.svg


Terraform Plan and Terraform plan out 
In other words we can call it as dry-run.
Terraform will detect any differences between your configuration and real physical resources that if exists.
We can see what changes Terraform is going to try to make using the Terraform plan command. 
You can create a plan file of the configuration with terraform plan -out=<filename>. 
This is useful if other users are modified some changes in main configuration file. 
So, after some time/days, when ever you want to create same resource, then you can use the create using this plan file.
The Plan file is in binary format. So can able to read it.

$ terraform plan -out=aws-instance-plan
Refreshing Terraform state in-memory prior to plan…

This plan was saved to: aws-instance-plan

To perform exactly these actions, run the following command to apply:
    terraform apply "aws-instance-plan"
$

Terraform Output:
Terraform output is used to get the variable values from terraform configuration after apply or state file.

$ cat instnace.tf
resource "aws_instance" "inst3" {
  ami           = "ami-03657b56516ab7912"
  instance_type = var.instance_type
}

output instance_type {
   value = var.instance_type
}

$ terraform output
instance_type = t2.micro
$

$ terraform output instance_type
t2.micro
$

Terraform Refresh:
Terraform refresh command will compare the status of real world infrastructure and with .state file.  Then when you run terraform plan, it will show if there is difference with state file and real world infrastructure.
In other view, if we have large infrastructure and so many resource. You want check for specific resource status. In that case, we can disable refresh flags.

terraform plan -refresh=false

The above command will not check refresh part. But it will give the changes and updates in the output.

Terraform Provisioners:

Provisioners can be used to performs some actions/scripts/tasks on the on created resources. This actions can performed on terraform managed/created resources.
If suppose, You want to execute some tasks like, install packages after creating instances resource. 
And, You want information like public ip, you want save it in your local machine or terminal. 
We have different types of provisioners. In that we will discuss about two types 1.remote-exec 2.local-exec
remote-exec : remote-exec type is used for installing / configuring it in the provisioned/created server. It means we are doing changes in remote server
Below is the example for the installing nginx web server on the aws instance using remote-exec provisioner

$ cat provisioner.tf
resource "aws_instance" "inst3" {
  #                  Ubuntu 16 aim id
  ami           = "ami-0f884a43c583aa2fb"
  instance_type = var.instance_type
  key_name      = "aws-keypair"

  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update -y",
      "sudo apt-get install nginx -y",
      "sudo ufw app list",
      "sudo ufw allow 'Nginx HTTP'",
      "sudo systemctl enable nginx",
      "sudo systemctl start nginx",
      "sudo systemctl restart nginx"
    ]
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ubuntu"
      private_key = file("aws-keypair.pem")
    }
  }
}
$
Note: in above example, we need to allow the traffic the 0.0.0.0/0 in default security group.

Local-exec provisioner will not be executed inside of remote server.
local-exec : local-exec type is that it will not perform the actions on top of the remote server/remote resource. It will just perform the actions on the remoter server and it will give results in your terminal.
Eg: you can get the attributes values(ip details) of the instance and you can store it in your local terminal.

$ cat provisioner-
provisioner-local-exec.tf   provisioner-remote-exec.tf
PRINHYLTPAP1074:aws-terraform shakeerp$ cat provisioner-local-exec.tf
resource "aws_instance" "inst4" {
  #                  Ubuntu 16 aim id
  ami           = "ami-0f884a43c583aa2fb"
  instance_type = var.instance_type

  provisioner "local-exec" {
    command = "echo ${aws_instance.inst4.private_ip} >> local-exec-provisoner-private-ip.txt"
    }
}
$

Terraform apply:

aws_instance.inst4: Creating...
aws_instance.inst4: Still creating... [10s elapsed]
aws_instance.inst4: Still creating... [20s elapsed]
aws_instance.inst4: Still creating... [30s elapsed]
aws_instance.inst4: Provisioning with 'local-exec'...
aws_instance.inst4 (local-exec): Executing: ["/bin/sh" "-c" "echo 172.31.37.209 >> local-exec-provisoner-private-ip.txt"]
aws_instance.inst4: Creation complete after 37s [id=i-023237466fa944e39]
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
$

$ cat local-exec-provisoner-private-ip.txt
172.31.37.209
$

   Issues in provisioner:
In some cases, due to some failures with commands, other configuration errors in provisioner block, resource completely not provisioned, in such cases , it will be tainted(means, it will recreate in next plan) by default.
So, in such cases to avoid tainting, we need to put “on_failure = continue” in provisioner block. This will be avoid taining the resource.
   provisioner "remote-exec" {
     on_failure = continue
     inline = [
Terraform Modules

Terraform modules are used to reduce the code.
We can use same resource code to multiple projects using modules.
We can centralize the terraform resources and we can call out this using modules from TF files whenever required in code.

Below is the sample example. Which is creating instance using module. 
We can write n numberer resource in module directory. Then we can call it in our project whenever required.
For each we need to run terraform init. For initialization but we can invoke number of resource.

In module dir:
$ cat ec2_module.tf
resource "aws_instance" "module_instance" {
  ami           = "ami-03657b56516ab7912"
  instance_type = var.inst_type
}

PRINHYLTPAP1074:ec2 shakeerp$
PRINHYLTPAP1074:ec2 shakeerp$ cat variables.tf
variable "inst_type" {
  default = "t2.micro"
}
$

In our project:
$ cat provider.tf
provider "aws" {
  region     = "us-east-2"
  access_key = "AKIAVA3BQ4IXJVJBGJQ2"
  secret_key = "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"
}
$

PRINHYLTPAP1074:project-A shakeerp$ cat module-local.tf
module "module_instance2" {
   source = "../modules/ec2/"
}
$


PRINHYLTPAP1074:aws-terraform shakeerp$ tree -a module-demo/
module-demo/
├── .terraform
│   └── modules
│       └── modules.json
├── modules
│   └── ec2
│       ├── ec2_module.tf
│       └── variables.tf
└── project-A
    ├── .terraform
    │   ├── modules
    │   │   └── modules.json
    │   └── plugins
    │       ├── registry.terraform.io
    │       │   └── hashicorp
    │       │       └── aws
    │       │           └── 3.13.0
    │       │               └── darwin_amd64
    │       │                   └── terraform-provider-aws_v3.13.0_x5
    │       └── selections.json
    ├── module-local.tf
    ├── provider.tf
    ├── terraform.tfstate
    └── terraform.tfstate.backup

13 directories, 10 files
$

Terraform Registry:
Terraform registry is repository of modules. Which are written by Terraform community.
In Terraform registry, we can find verified and not verified modules by terraform.
If it is verified module by terraform hashicorp, you can see a blue badge beside of module.
We have so many registry modules for resource like AWS VPC, RDS, ELB and etc.

Below example will create the instance using terraform registry aws ec2-instance module.

Terrafrom Registry : https://registry.terraform.io/modules/terraform-aws-modules/ec2-instance/aws/latest

$ cat provider.tf
provider "aws" {
  region     = "us-east-2"
  access_key = "AKIAVA3BQ4IXJVJBGJQ2"
  secret_key = "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"
}
$
$ cat registry-module-demo.tf
module "ec2_cluster" {
  source                 = "terraform-aws-modules/ec2-instance/aws"
  version                = "~> 2.0"

  name                   = "my-instance1"
  instance_count         = 1

  ami                    = "ami-03657b56516ab7912"
  instance_type          = "t2.micro"
  subnet_id              = "subnet-29344b65"

  tags = {
    Terraform   = "true"
    Environment = "dev"
  }
}
$


PRINHYLTPAP1074:aws-terraform shakeerp$ tree -a registry-module
registry-module
├── .terraform
│   ├── modules
│   │   ├── ec2_cluster
│   │   │   ├── .chglog
│   │   │   │   ├── CHANGELOG.tpl.md
│   │   │   │   └── config.yml
│   │   │   ├── .editorconfig
│   │   │   ├── .git
│   │   │   │   ├── HEAD
│   │   │   │   ├── config
│   │   │   │   ├── description
│   │   │   │   ├── hooks
│   │   │   │   │   ├── applypatch-msg.sample
│   │   │   │   │   ├── commit-msg.sample
│   │   │   │   │   ├── fsmonitor-watchman.sample
│   │   │   │   │   ├── post-update.sample
│   │   │   │   │   ├── pre-applypatch.sample
│   │   │   │   │   ├── pre-commit.sample
│   │   │   │   │   ├── pre-push.sample
│   │   │   │   │   ├── pre-rebase.sample
│   │   │   │   │   ├── pre-receive.sample
│   │   │   │   │   ├── prepare-commit-msg.sample
│   │   │   │   │   └── update.sample
│   │   │   │   ├── index
│   │   │   │   ├── info
│   │   │   │   │   └── exclude
│   │   │   │   ├── logs
│   │   │   │   │   ├── HEAD
│   │   │   │   │   └── refs
│   │   │   │   │       ├── heads
│   │   │   │   │       │   └── master
│   │   │   │   │       └── remotes
│   │   │   │   │           └── origin
│   │   │   │   │               └── HEAD
│   │   │   │   ├── objects
│   │   │   │   │   ├── info
│   │   │   │   │   └── pack
│   │   │   │   │       ├── pack-dd54c5499f19df52528799ed30aa3be65e66f572.idx
│   │   │   │   │       └── pack-dd54c5499f19df52528799ed30aa3be65e66f572.pack
│   │   │   │   ├── packed-refs
│   │   │   │   └── refs
│   │   │   │       ├── heads
│   │   │   │       │   └── master
│   │   │   │       ├── remotes
│   │   │   │       │   └── origin
│   │   │   │       │       └── HEAD
│   │   │   │       └── tags
│   │   │   ├── .gitignore
│   │   │   ├── .pre-commit-config.yaml
│   │   │   ├── CHANGELOG.md
│   │   │   ├── LICENSE
│   │   │   ├── Makefile
│   │   │   ├── README.md
│   │   │   ├── examples
│   │   │   │   ├── basic
│   │   │   │   │   ├── README.md
│   │   │   │   │   ├── main.tf
│   │   │   │   │   └── outputs.tf
│   │   │   │   └── volume-attachment
│   │   │   │       ├── README.md
│   │   │   │       ├── main.tf
│   │   │   │       └── outputs.tf
│   │   │   ├── main.tf
│   │   │   ├── outputs.tf
│   │   │   └── variables.tf
│   │   └── modules.json
│   └── plugins
│       ├── registry.terraform.io
│       │   └── hashicorp
│       │       └── aws
│       │           └── 3.13.0
│       │               └── darwin_amd64
│       │                   └── terraform-provider-aws_v3.13.0_x5
│       └── selections.json
├── provider.tf
├── registry-module-demo.tf
└── terraform.tfstate

29 directories, 48 files
$

Terraform Workspace
Terraform workspace like different environments in single window. 
If you assume, if your working for the development environment, according to the environment you can create your workspace, environment variables. In the same for the other workspaces.
Terraform allow us to have multiple workspaces, with each workspace we can have different environment variables associated.

In the below example, according to workspace the values will assigned to the variables. This is just examples to understand.
Eg: we have defined the instance-type values for different workspaces. instance_type value in terraform plan command, will change accordingly for workspace.



 $ cat workspace-example.tf
variable "instancetype" {
   type = map
   default = {
      default = "t2.micro-default"
      production = "t2.micro-production"
      development = "t2.micro-development"
   }
}

resource "aws_instance" "workspace_instance" {
  ami                    = "ami-03657b56516ab7912"
  instance_type          = lookup(var.instancetype,terraform.workspace)
}

$

$ terraform workspace list
  default
* development
  production

$
$ terraform plan | grep instance_type
      + instance_type                = "t2.micro-development"
$
$ terraform workspace select production
Switched to workspace “production".
$
$ terraform workspace show
production
$
$ terraform plan | grep instance_type
      + instance_type                = "t2.micro-production"
$

Overview of directory after creating workspace:

$ tree -a workspace-terraform/
workspace-terraform/
├── .terraform
│   ├── environment
│   └── plugins
│       ├── registry.terraform.io
│       │   └── hashicorp
│       │       └── aws
│       │           └── 3.13.0
│       │               └── darwin_amd64
│       │                   └── terraform-provider-aws_v3.13.0_x5
│       └── selections.json
├── provider.tf
├── terraform.tfstate.d                   #### This directory will be generated and it is used to store the different workspace state files.
│   ├── development
│   │   ├── terraform.tfstate
│   │   └── terraform.tfstate.backup
│   └── production
│       └── terraform.tfstate
└── workspace-example.tf

10 directories, 8 files
$

Note: For deleteting or destroying, you need to switch individually and destroy the resources.


Commands:

$ terraform workspace -h
Usage: terraform workspace

  new, list, show, select and delete Terraform workspaces.

Subcommands:
    delete    Delete a workspace
    list      List Workspaces
    new       Create a new workspace
    select    Select a workspace
    show      Show the name of the current workspace
$

$ terraform workspace list
* default

$
$ terraform workspace new development
Created and switched to workspace "development"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
$
$ terraform workspace new production
Created and switched to workspace "production"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
$
$ terraform workspace list
  default
  development
* production
$

$ terraform workspace select development
Switched to workspace "development".

$ terraform workspace list
  default
* development
  production

$



Terraform Git:
S


Terraform State Management Remotely:

By default, Terraform stores state locally in a file named terraform.tfstate. When working with Terraform in a collaborate team, storing it in local is complicated because each user must make sure they always have the latest state data before running Terraform commands and make sure that nobody else runs Terraform at the same time.
In such case, we use Terraform remote state management.
With remote state, Terraform writes the state data to a remote data store, which can then be shared between all members of a team.
Terraform supports storing state in Terraform cloud,  Amazon S3, Azure Blob Storage, Google Cloud Storage, Alibaba Cloud OSS, and more.

In below example, we are storing storing in s3 backend.
we can store the the .tfstate file in remote place like in S3 bucket. This storied the instace created state file and other resources.
I am storing the testate file in already created s3 bucket “tf-s3-bucket-shakeer”. 


$ cat provider.tf
provider "aws" {
  region     = "us-east-2"
  access_key = "xxx"
  secret_key = "xxx"
}
$ cat instnace.tf
resource "aws_instance" "inst3" {
  ami           = "ami-03657b56516ab7912"
  instance_type = "t2.micro"
}
$
$ cat backend.tf
terraform {
  backend "s3" {
    bucket = "tf-s3-bucket-shakeer"
    key    = "terraformdemo.tfstate"
    region = "us-east-2"
    access_key = "xxx"
    secret_key = "xxx"
  }
}
$
$ tree -a remote-backend
remote-backend
├── .terraform
│   ├── plugins
│   │   ├── registry.terraform.io
│   │   │   └── hashicorp
│   │   │       └── aws
│   │   │           └── 3.14.0
│   │   │               └── darwin_amd64
│   │   │                   └── terraform-provider-aws_v3.14.0_x5
│   │   └── selections.json
│   └── terraform.tfstate
├── backend.tf
├── instnace.tf
└── provider.tf

7 directories, 6 files
$



Terraform State Locking:

If two or more users are using or trying to do any operations at same time then there will be some error. In such case state file will be corrupted. In such case we use state locking feature will be useful.

Multiple users are working on same project. Sharing same file. In such cases we need to maintaining state file remotely.

In some case, if a user forget to enter yes after ran the command terraform apply. Then state file lis locked until the operation is completed.
Then we use the force-unlock command to unlock the state file.

terraform force-unlock c8324752-fcf9-bb6f-dcf0-d42ad785588a

This will be unlocked the you can apply the things.

Notes : 
Unlocking feature will not available for local operation. Available for remote backends.
You will get error like this : Local state cannot be unlocked by another process
Locking feature is not available for all the backend types.  Currently in remote backend s3 with dynamoDB will be available.
https://www.youtube.com/watch?v=BlRJpLoduB4


Terraform Multiple Regions and Multiple account:

If you want to create resources in multiple regions. In that case we need to add the "alias" parameter into the configuration.

provider "aws" {
  region     =  “us-west-1”
}
provider "aws" {
  alias      =  "aws02"
  region     =  "ap-south-1”
}
resource "aws_eip" "myeip" {
  vpc = "true"
}

resource "aws_eip" "myeip01" {
  vpc = "true"
  provider = "aws.aws02”
}
The above configuration will create the resources in different regions.

Multiple accounts:

If you want to create resources in multiple aws accounts. 
You configure AWS CLI in your account. And, you two different accounts in aws. Then if you want to create 



```
