project=myproject
# export AWS_ACCESS_KEY_ID=
# export AWS_SECRET_ACCESS_KEY=
env=test
aws=/Users/vyscond/.pip-env/awscli/bin/aws
aws_cloudformation_stack_name=$(project)-$(env)
aws_cloudformation_file=cloudformation/init.yaml

spinup:
	$(aws) cloudformation create-stack \
		--stack-name $(aws_cloudformation_stack_name) \
		--capabilities \
			CAPABILITY_AUTO_EXPAND \
		--parameters \
			ParameterKey=ProjectName,ParameterValue=$(project) \
		--template-body file://$(aws_cloudformation_file)
	$(aws) cloudformation wait stack-create-complete  \
		--stack-name $(aws_cloudformation_stack_name)

spindown:
	$(aws) cloudformation delete-stack \
		--stack-name $(project)-$(env)
	$(aws) cloudformation wait stack-delete-complete \
		--stack-name $(aws_cloudformation_stack_name)
