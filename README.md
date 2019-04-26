# Cookiecutter aws-ecs-cluster

Cookiecutter is a CLI utility that bootstraps projects from existing templates. **This repository contains an opinionated cookiecutter template to deploy an ECS cluster with ALB load balancers.** A project instantiated from this template can be used a hosting layer for other cookiecutters, such as [cookiecutter-java-docker](https://bitbucket.org/persgroep/cookiecutter-java-docker)

## Getting Started

### Prerequisites

In order to run this cookiecutter template, you need to install following:

#### \*nix an OS X

- python 3 (`check: python3 --version`)
- [Cookiecutter](https://github.com/audreyr/cookiecutter):
	```
	pip3 install cookiecutter
	```
- aws cli (`check: aws --version`)
- make tools (`make --version`)

#### Windows

On Windows, with [chocolatey](https://chocolatey.org) and [sudo](https://chocolatey.org/packages/sudo) installed 
```
sudo choco install python
sudo choco install awscli
pip install cookiecutter
```
For make and other Unix Utilities, you can download [unxUtils.1.0.0.1.nupkg](https://artifactory.persgroep.cloud/artifactory/ext-release-local/org/chocolatey/UnxUtils/1.0.0.1/unxUtils.1.0.0.1.nupkg)
```
 sudo choco install unxUtils.1.0.0.1.nupkg
```
#### Required AWS Resources

- **AWS CLI:** you should be logged in (using onelogin).
- **Artefacts S3 bucket:** this bucket needs to be versioned.
- **Logs bucket stack:** created from [https://bitbucket.org/persgroep/aws-iac-modules/src/master/cloudformation/storage/s3_log_bucket.yaml](https://bitbucket.org/persgroep/aws-iac-modules/src/master/cloudformation/storage/s3_log_bucket.yaml)
- **IAM user for the Bitbucket pipeline**
	- This user should have only **"Programmatic access"** and **NO** "AWS Management Console access".
	- The user should have only write access to your artefacts bucket.

*IAM user policy example:*
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ArtefactsBucketAccess",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::REPLACE-with-artefacts-bucket-name",
                "arn:aws:s3:::REPLACE-with-artefacts-bucket-name/*"
            ]
        }
    ]
}
```

### Configuration

**Create a `.cookiecutterrc` file in your home directory.** Cookiecutter will use the values from this file as defaults when executing templates.

Here's an axample of a `.cookiecutterrc` file:
```
default_context:
    # AWS settings
    aws_account_id: 123456789012
    aws_default_region: eu-west-1
    squad_name: squadname

    # Project settings
    project_name: aws-shared-infra
    project_short_description:  ECS shared infrastructure
    cloudformation_stack_prefix: shared1

    # Global settings
    artifact_s3_bucket: dpp-artifacts-123456789012-eu-west-1-cfn
    log_bucket: dpp-logs-123456789012-eu-west-1-cfn
    nonprod_vpc_id: vpc-05fa69ba5418644d0
    nonprod_private_subnets: subnet-05d97978360bbf284,subnet-0402dd4a8b2f9f1c5,subnet-08dda5b9666a7d78a
    nonprod_public_subnets: subnet-0d0637c225e99b181,subnet-0afa0895deffd2da7,subnet-091532bd37e5fad92
    prod_vpc_id: vpc-0ae3bfd370f7710c9
    prod_private_subnets: subnet-0fa6f94ad6a12f442,subnet-0ce1b90c33aac972d,subnet-062e66b2196de6ff7
    prod_public_subnets: subnet-0743eaf0a96819de6,subnet-0c0cf652bb81a6e15,subnet-08a892565e8573f6f
    deploy_public_alb: Y
    deploy_private_alb: Y
    ssl_certificate_arn: arn:aws:acm:eu-west-1:123456789012:certificate/9ff25a70-da6c-4173-b47d-8274acaf0074
    sns_alert_topic": arn:aws:sns:eu-west-1:123456789012:alerts-squadname
```

*Note: depending on the environments in your AWS account you should/could provide empty values for the prod or nonprod variables (don't omit these variables as this will break Cookiecutter, use empty strings as value instead).*

#### aws\_account\_id
The AWS account to deploy the ECS cluster in.
#### aws\_default\_region
The AWS region to deploy the  ECS cluster in.
#### squad\_name
Your team/squad name.
#### project\_name:
The name for the project. Cookiecutter will make a slug out of it by making it all lowercase and by replacing
whitespaces with dashes. Furthermore this slug will be used for naming resources and as you repository name.
#### project\_short\_description
The project's description.
#### cloudformation\_stack\_prefix
The prefix used to properly name the CloudFormation resources.
#### artifact\_s3\_bucket
The S3 bucket to store the artefacts.
#### log\_bucket
The S3 bucket to store the logs.
#### nonprod\_vpc\_id
The VPC ID for non-production.
#### nonprod\_private\_subnets
The private subnets of the non-production VPC.
#### nonprod\_public\_subnets
The public subnets of the non-production VPC.
#### prod\_vpc\_id
The VPC ID for production.
#### prod\_private\_subnets
The private subnets of the production VPC.
#### prod\_public\_subnets
The public subnets of the production VPC.
#### deploy\_public\_alb
'Y' to deploy the public Application Load Balancer
#### deploy\_private\_alb
'Y' to deploy the private Application Load Balancer
#### ssl\_certificate\_arn
The SSL certifacte for the Application Load Balancer Lister
#### sns\_alert\_topic
The SNS Topic for alerting

## Usage

1. Run cookiecutter in the directory where you usually checkout your git repo's. Cookiecutter will create a new sub-folder in this directory. You'll be 
prompted with a number of questions to help bootstrapping the new project.

        $ cookiecutter -f git@bitbucket.org:persgroep/cookiecutter-aws-ecs-cluster.git
1. Move to the newly created project

        $ cd yet-another-test-project

1. Enable git for this project:

        git init
        git add .
        git commit -m "Initial setup"

1. Create a new repository in Bitbucket.

1. Push your code to BitBucket.

        git remote add origin git@bitbucket.org:persgroep/yet-another-test-project.git
        git push -u origin master

1. Enable **Bitbucket pipelines** for your new repository.
    - Enable pipelines:`Settings` > `Pipelines` > `Settings` > Enable Pipelines
    - Add environment variables:
 		- `Settings` > `Pipelines` > `Repository variables` and add:
        	- `AWS_ACCESS_KEY_ID`
        	- `AWS_SECRET_ACCESS_KEY` (as secured variable).
        	- `AWS_DEFAULT_REGION` (most likely 'eu-west-1')

1. Deploy the Pipeline. (this step also creates the ECR repository to contain the docker images).

        make deploy-pipeline
        
	***Important note: this is a typical "chicken or egg" case. The AWS CodePipeline will fail initially because of a missing artefact. This is normal! Running the Bitbucket pipeline will create the missing artefact and fix the CodePipeline.***
	
1. Push a change to Bitbucket to trigger the Bitbucket pipeline (which will then trigger the AWS CodePipeline). The AWS CodePipeline should now create your ECS cluster(s). 

## Contributing
This cookiecutter template is **Community Driven**, so everybody within De Persgroep is free to contribute. Read the [Contributors' Guide](CONTRIBUTING.md) for details on how-to contribute.

## Architecture

*Note: depending on the options you choose when executing the template certain components will not be deployed. The diagram below reflects the situation when* ***ALL*** *components are deployed.*

![Architecture overview](img/Cookiecutter_ecs.png)

*This image can be edited with [draw.io](https://www.draw.io/). Find the source under /img/Cookiecutter_ecs.html.*



	





