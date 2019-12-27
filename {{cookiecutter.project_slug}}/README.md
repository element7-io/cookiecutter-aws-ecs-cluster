# {{ cookiecutter.project_name }}

{{ cookiecutter.project_short_description }}

## Resources
- CodePipeline: **{{ cookiecutter.project_slug }}-pipeline-cfn**
- Artefact S3 bucket: **{{ cookiecutter.artifact_s3_bucket }}**

## Installation

### CodePipeline
To setup a pipeline for installing and maintaining the ECS Cluster deploy the CloudFormation stack `cfn-pipeline.yaml`:

1. Login to the AWS CLI
1. Create a new GitHub personal access token. See [here](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for how to do this - CodePipeline needs just the `repo` scope permissions. :warning: The user associated with the personal access token above **MUST** have administrative rights for the Github repository.
1. run: `make deploy-pipeline GITHUB_TOKEN=your_github_personal_access_token`

More information about using Github as a Source for AWS CodePipeline can be found here:  [Create a Webhook for a GitHub Source (AWS CloudFormation Template)](https://docs.aws.amazon.com/codepipeline/latest/userguide/pipelines-webhooks-create-cfn.html)

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
