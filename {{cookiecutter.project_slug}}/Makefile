
CF_STACK_NAME={{ cookiecutter.pipeline_stackname }}
PROJECT_NAME={{ cookiecutter.project_slug }}
ARTIFACT_BUCKET={{ cookiecutter.artifact_s3_bucket }}

SHELL=bash
GREEN="\n\n\033[0;32m"
YELLOW="\033[0;33m"
RED="\033[0;31m"
END=" \033[0m\n"

clean:
	@echo -e $(GREEN)Clean up...$(END)
	test ! -f ${PROJECT_NAME}.zip || rm -f ${PROJECT_NAME}.zip
.PHONY: clean

build-zipfile:
	@echo -e $(GREEN)Building zip file...$(END)
	mkdir -p artefacts/${PROJECT_NAME}
	test ! -d dist || rm -rf dist
	mkdir dist dist/config
	cp -v -r cfn-*.yaml config/ dist/
	cd dist && zip -r ../artefacts/${PROJECT_NAME}/${PROJECT_NAME}.zip *
	@echo ""
.PHONY: build-zipfile

# Important note: this is a typical "chicken or egg" case. The AWS CodePipeline will fail
# initially because of a missing artefact. This is normal! Running the Bitbucket pipeline will
# create the missing artefact and fix the CodePipeline.
deploy-pipeline:
	@echo -e $(GREEN)Deploying Pipeline...$(END)
	aws cloudformation create-stack --stack-name ${CF_STACK_NAME} \
		--template-body file://cfn-pipeline.yaml \
		--enable-termination-protection \
		--capabilities CAPABILITY_IAM \
		--timeout-in-minutes 15

	@echo ""
	@echo Waiting until Pipeline deployed...
	@aws cloudformation wait stack-exists --stack-name ${CF_STACK_NAME}
	@aws cloudformation wait stack-create-complete --stack-name ${CF_STACK_NAME}
	@echo ""
	@echo -e $(YELLOW)This newly created AWS CodePipeline will initialy $(RED)FAIL $(YELLOW)due to a missing artefact, this is expected behaviour!
	@echo -e You will need to invoke the Bitbucket pipline to create the msssing artefact and fix the AWS CodePipeline.$(END)
	@echo ""
.PHONY: deploy-pipeline
