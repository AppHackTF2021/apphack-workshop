# Apphack Terraform Challenge
In this Terraform challenge, you will have to deploy a few infrastructure components to a major cloud provider: [Amazon Web Services](https://aws.amazon.com/). You do not need to be an expert on AWS in order to complete this workshop, as the focus is to learn Terraform. However, any experience in any of these platforms will certainly be helpful.

In order to help you build the final solution, this challenge will have different stages. The more stages you go through, the more complex your infrastructure will become and the closer you will be to the final solution.

For this challenge, we will be using [Terraform](https://www.terraform.io/docs/index.html) **0.14.7**.

# FAQ

## I did not attend the Apphack workshop. How can I take this challenge?

To take this challenge, you will have to provision the same base infrastructure Slalom Build provided the attendees during the event. We may provide a tutorial for you to follow in this repo to provision your own base infrastructure if time allows.

## I heard there are later versions of Terraform coming out. Will we use them?

No, we'll proceed with version `0.14.7` for simplicity purposes this time

# Getting started

You will not need to install Terraform, the AWS CLI or any other packages on your computers to complete this challenge. You will be provided with a virtual environment which already contains all the tools needed.

To get started, open up a web browser of you choice and navigate to [AWS URL]. Use the team name and password we provided you to sign in to the AWS console
...

# Stage 0

This stage will get you familiar enough with Terraform so you can start building the solution in the next stages. **However, if you are already experienced in using Terraform, you can skip to Stage 1**.

First, open the Cloud9 workspace you located in the **Getting Started** section and create two files called **providers.tf** and  **outputs.tf** with the following content:

providers.tf
```
terraform {
  # This module is now only being tested with Terraform 0.14.x - we are setting
  # 0.14.1 as the minimum version
  required_version = ">= 0.14.1"
}

# The default provider configuration; resources that begin with `aws_` will use
# it as the default, and it can be referenced as `aws`.
provider "aws" {
  region = "us-east-1"
}
```

outputs.tf
```
# The simplest possible Terraform module: it just outputs "Hello, World!"
output "intro" {
  value = "Hello, World!"
}
```

Terraform template files have *.tf* extension. That's where you will define all the resources your infrastructure will have.
The provider block you defined in providers.tf will tell Terraform which plugins to download. In this case, Terraform will download the AWS plugin so it can make calls to the AWS API (Terraform does not use the AWS CLI to make API calls, it actually uses the [AWS Golang SDK](https://github.com/terraform-providers/terraform-provider-aws/tree/master/aws)).
The resource block (with syntax: *output "RESOURCE_NAME" {}*), on the other hand, is used to print or return values of a Terraform module, and has several uses. In this case, you will be creating a basic output as an example.

Once you finish output.tf, the first command you will run is *terraform init*:

```
$ terraform init

Initializing provider plugins...
- Checking for available provider plugins on https://releases.hashicorp.com...
- Downloading plugin for provider "aws" (1.28.0)...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.aws: version = "~> 1.28"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

What *terraform init* does is it creates a directory called *.terraform* and downloads the AWS plugin. That means if you declare other providers, like GCP, *terraform init* will also download the GCP plugin.

You will also need to rerun *terraform init* whenever you:

* Declare a provider
* Declare a module
* Configure a remote backend

Next, let's see how we can perform a dry run. Run the following command:

```
$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
...

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

The goal of Terraform plan is to tell you what will be created, modified or deleted. That's just so you are aware what will happen with your infrastructure before you go ahead and apply the changes.

Once you're happy with plan's output, run:

```
$ terraform apply
```

After you ran apply, Terraform created a file called *terraform.tfstate*. A [state file](https://www.terraform.io/docs/state/) contains state information about your managed infrastructure and configuration. If you cat terraform.tfstate, you will see the following:
```
```

The state file keeps track of things like the Terraform version, the resources created, resource dependencies etc. When working in a bigger project, you do not want to store the state file locally. The best approach is to save this file in a remote storage (like S3). You will see how this is done as you progress in the challenge.

Let's destroy the infrastructure created:
```
$ terraform destroy
```
Now you're ready to begin the challenge. **Keep both files, but you may remove the hello_world block from outputs.tf if you wish**.

# Stage 1

## Architecture
![Stage 1](./images/stage-1.png)

In the first stage, you will launch a very basic AWS resource - an S3 bucket that will later serve a higher purpose. This is obviously far from a secure solution, but this is just the first step. Use the following information for the AWS resources you will need to define in your Terraform template files:

* Provider
* Resource
* Use tags to name your resources and to identify your team as owner of the resource. For example, within each Terraform resource, declare a tags block (if the resource supports tags) with Name and Team keys:

```
resource "aws_s3_bucket" "teambucket" {
  bucket = "apphack-team-X-bucket"
  acl    = "private"

  tags = {
    Name        = "Name of the reosurce"
    Team = "Team X"
  }
}
```
Note: You'll need to replace the 'X' in this block to the actual team number you were assigned prior to beginning this challenge

## Output

Since you will not have access to the AWS Console, there needs to be a way for you to find out the IP address of your instance. Use [Output Variables](https://www.terraform.io/intro/getting-started/outputs.html) for that.

## DoD (Definition of Done)

You will have completed the first Stage if you go to your web browser, navigate to the S3 service-. This means you have successfully deployed your team's S3 bucket.

# Stage 2

## Static Website Hosting

## DoD (Definition of Done)

# Stage 3

## Simple Notification Service

Note: don't spam other peoples' emails to troll. If we catch you doing this we will delete you cloud9 and you'll be done

## DoD (Definition of Done)

# Stage 4

## S3 Bucket Notifications

## DoD (Definition of Done)

# Stage 5 (BONUS!)

## Variables and Organization

Since you have completed the challenge successfully, you earned a bonus stage!

One of the best practices when developing Terraform templates is to organize the files into separate components. Sometimes a Terraform workspace looks like:

```
main.tf
```

Where all the infrastructure is defined in main.tf. This is similar to developing an entire API using a single file. It's not scalable. On the other hand, some workspaces might look like:

```
network.tf
api.tf
database.tf
main.tf
```

This is a better approach since the infrastructure is being split into components. Here's what you will have to do in Stage 6. First, restructure your workspace to look like the following:

```
.
├── main.tf
├── variables.tf
├── provider.tf
├── outputs.tf
└── terraform.tfvars
```

Whenever you use a variable (with the syntax "${var.XXXX}"), you need to declare this variable (**variable "XXXX" {}**) either in the same or in a different .tf file (where the variable is declared doesn't matter as Terraform loads all .tf files). This means you could potentially declare *ami_id* and *subnet_id* in main.tf, but when the number of variables starts to increase, it's cleaner to put them in a separate file.

### Ok, but what about terraform.tfvars? What is that for?

If you take a look at the main.tf above, you will see that both ami_id and subnet_id were hardcoded. What if you want to use these values somewhere else in main.tf? It's certainly not advisable that you keep copying and pasting. So, variables to the rescue!

The same way that we made ami_id and subnet_id variables in the EC2 module, we will make them variables in main.tf as well:

```
variables "ami_id"    {}
variables "subnet_id" {}

module "ec2" {
  source    = "./modules/ec2"
  ami_id    = "${var.ami_id}"
  subnet_id = "${var.subnet_id}"
}
```

(You declared the variables in main.tf. Can I declare them in a separate file called variables.tf? **Yes**).

Now that ami_id and subnet_id are variables for the main template file, we need to write their value somewhere. There are three main options that can help you achieve that. If you leave your workspace as is (without a **terraform.tfvars** file), whenever you run **terraform plan**, Terraform will ask you for the value of these two variables. The second option is to provide the value of these variables via cli:

```
terraform plan -var "ami_id=ami-xxxxx" -var "subnet_id=subnet-xxxxx"
```

But for a large number of variables, it'd be a tedious job to declare them all via cli. The third and best option is to write the values in a file which Terraform will read. That's **terraform.tfvars**. The syntax of a terraform.tfvars is simple:

```
ami_id = "ami-xxxxx"
subnet_id = "subnet-xxxxx"
```

It's simply key-value pairs. Now whenever you do terraform plan, you will not be asked for variable values.

PS: terraform.tfvars is a special name which Terraform knows about. If you call it something else (e.g., infrastructure.tfvars), you will need to tell Terraform which file to look at by using the cli option **-var-file**:

```
terraform plan -var-file=infrastructure.tfvars
```

Hopefully that will help you restructure your workspace following best practices. As always, let one of the organizers know if you have any issues.

## DoD (Definition of Done)

Same as Stage 5.

### Good luck to all teams!
