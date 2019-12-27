# {{ cookiecutter.project_name }}

{{ cookiecutter.project_short_description }}

## Resources
- CodePipeline: **{{ cookiecutter.project_slug }}-pipeline-cfn**
- Artefact S3 bucket: **{{ cookiecutter.artifact_s3_bucket }}**

## Installation

### CodePipeline
To setup a pipeline for installing and maintainingthe ECS Cluster deploy the CloudFormation stack `cfn-pipeline.yaml`:

1. Login to the AWS CLI
1. run: `make deploy-pipeline`

## Usage
This repository contains a series of re-usable/shared resources that can be used to host projects initiated from [cookiecutter-java-docker](https://bitbucket.org/persgroep/cookiecutter-java-docker).

You can add any additional resource (eg. RDS instance) to the `cfn-template.yaml` template file.
After committing your changes the pipeline will automatically deploy these changes on CloudFormation.

Bitbucket pipelines are used to build a zipfile containing the CloudFormation template and stack configuration files. This zipfile is uploaded onto the artefact S3 bucket.

To add additional shared resources:

* Update the CF template `cfn-template.yaml`.
* Commit, merge and push the changes.

To change configuration details:

* Update the corresponding parameter(s) in the json files in the `config` directory.
* Commit, merge and push the changes.
