SHELL=bash
GREEN="\n\n\033[0;32m"
YELLOW="\033[0;33m"
RED="\033[0;31m"
END=" \033[0m\n"

CF_STACK_NAME={{ cookiecutter.pipeline_stackname }}
PROJECT_NAME={{ cookiecutter.project_slug }}

check_defined = \
		$(strip $(foreach 1,$1, \
        $(call __check_defined,$1,$(strip $(value 2)))))
__check_defined = \
    $(if $(value $1),, \
      $(error Undefined $1$(if $2, ($2))))

deploy-pipeline:
	@:$(call check_defined, GITHUB_TOKEN)
	@echo -e $(GREEN)Deploying Pipeline...$(END)
	aws cloudformation create-stack --stack-name ${CF_STACK_NAME} \
		--template-body file://cfn-pipeline.yaml \
		--enable-termination-protection \
		--capabilities CAPABILITY_IAM \
		--parameters \
			ParameterKey=GitHubOAuthToken,ParameterValue=${GITHUB_TOKEN} \
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
