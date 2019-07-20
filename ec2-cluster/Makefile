project=dockerswarmcluster

aws=aws
bastion_username=centos
aws_key_name=$(project)-$(env)
aws_cloudformation_stack_name=$(project)-$(env)
aws_cloudformation_file=cloudformation/init.yaml
env=test

ifeq ($(env),test)
endif

keypair_create:
	mkdir -p $(project)/
	aws ec2 create-key-pair \
		--query "KeyMaterial" \
		--output=text \
		--key-name $(aws_key_name) > $(project)/$(aws_key_name).pem
	chmod 0400 $(project)/$(aws_key_name).pem

keypair_delete:
	$(aws) ec2 delete-key-pair --key-name $(aws_key_name)
	rm -f $(project)/$(aws_key_name).pem

spinup: keypair_create
	$(aws) cloudformation create-stack \
		--stack-name $(aws_cloudformation_stack_name) \
		--capabilities \
			CAPABILITY_AUTO_EXPAND \
		--parameters \
			ParameterKey=KeyPairName,ParameterValue=$(aws_key_name) \
			ParameterKey=ProjectName,ParameterValue=$(project) \
		--template-body file://$(aws_cloudformation_file)
	$(aws) cloudformation wait stack-create-complete  \
		--stack-name $(aws_cloudformation_stack_name)

spindown: keypair_delete
	$(aws) cloudformation delete-stack \
		--stack-name $(project)-$(env)
	$(aws) cloudformation wait stack-delete-complete \
		--stack-name $(aws_cloudformation_stack_name)

bastion_shell:
	$(eval bastion_ip := $(shell $(aws) ec2 describe-instances --query "Reservations[*].Instances[*].PublicIpAddress" --output=text --filters Name=tag:ClusterRole,Values=Bastion))
	ssh $(bastion_username)@$(bastion_ip) -i $$PWD/$(project)/$(aws_key_name).pem