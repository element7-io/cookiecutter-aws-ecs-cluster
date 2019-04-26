# {{ cookiecutter.project_name }}

{{ cookiecutter.project_short_description }}

## Resources

* CodePipeline: {{ cookiecutter.project_slug }}-pipeline-cfn
* Artifact S3 bucket: {{ cookiecutter.artifact_s3_bucket }}

The CodePipeline resource is automatically created when the `cfn-pipeline.yaml` is deployed onto AWS (`make deploy-pipeline`).

## Usage

This repository contains a series of re-usable/shared resources that can be used to host projects initiated from [cookiecutter-java-docker](https://bitbucket.org/persgroep/cookiecutter-java-docker).

If additional shared resources (eg. RDS instance) would be necessary, they should be added to the `cfn-template.yaml` template file. The Pipeline will automatically pick these changes up and reflect them in the deployed stacks.

Bitbucket pipelines are used to build a zipfile containing the CF template and stack configuration files. This zipfile is uploaded onto an Artifact S3 bucket.

To add additional shared resources:

* Update the CF template `cfn-template.yaml`.
* Commit, merge and push the changes.

To change configuration details:

* Update the corresponding parameter(s) in the json files in the `config` directory.
* Commit, merge and push the changes.
