.ONESHELL:
project=XYZ

# Bins
aws=aws

# Envs
env=test

# Vars
stack_name=$(project)-$(file)


spinup:
	$(aws) cloudformation create-stack \
		--stack-name $(stack_name) \
		--parameters \
			ParameterKey=ProjectName,ParameterValue=$(project) \
		--template-body file://$(file).yaml

	$(aws) cloudformation wait stack-create-complete  \
		--stack-name $(stack_name)

spindown:
	$(aws) cloudformation delete-stack \
		--stack-name $(stack_name)

	$(aws) cloudformation wait stack-delete-complete \
		--stack-name $(stack_name)

respin: spindown spinup
