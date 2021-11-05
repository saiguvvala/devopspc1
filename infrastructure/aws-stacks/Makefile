ENVIRONMENT = sbx
PREFIX = niroop
PROFILE = default
KEYNAME = LaunchTempateEc2
DBUSERNAME = niroop
DBPASSWORD = niroop1234
ARTIFACTBUCKETNAME = nirooptestbucket

.PHONY: explain
explain:
	#                                                                                                                                                        
		# PPPPPPPPPPPPPPPPP   IIIIIIIIIIPPPPPPPPPPPPPPPPP   EEEEEEEEEEEEEEEEEEEEEELLLLLLLLLLL             IIIIIIIIIINNNNNNNN        NNNNNNNNEEEEEEEEEEEEEEEEEEEEEE
		# P::::::::::::::::P  I::::::::IP::::::::::::::::P  E::::::::::::::::::::EL:::::::::L             I::::::::IN:::::::N       N::::::NE::::::::::::::::::::E
		# P::::::PPPPPP:::::P I::::::::IP::::::PPPPPP:::::P E::::::::::::::::::::EL:::::::::L             I::::::::IN::::::::N      N::::::NE::::::::::::::::::::E
		# PP:::::P     P:::::PII::::::IIPP:::::P     P:::::PEE::::::EEEEEEEEE::::ELL:::::::LL             II::::::IIN:::::::::N     N::::::NEE::::::EEEEEEEEE::::E
		#  P::::P     P:::::P  I::::I    P::::P     P:::::P  E:::::E       EEEEEE  L:::::L                 I::::I  N::::::::::N    N::::::N  E:::::E       EEEEEE
		#  P::::P     P:::::P  I::::I    P::::P     P:::::P  E:::::E               L:::::L                 I::::I  N:::::::::::N   N::::::N  E:::::E             
		#  P::::PPPPPP:::::P   I::::I    P::::PPPPPP:::::P   E::::::EEEEEEEEEE     L:::::L                 I::::I  N:::::::N::::N  N::::::N  E::::::EEEEEEEEEE   
		#  P:::::::::::::PP    I::::I    P:::::::::::::PP    E:::::::::::::::E     L:::::L                 I::::I  N::::::N N::::N N::::::N  E:::::::::::::::E   
		#  P::::PPPPPPPPP      I::::I    P::::PPPPPPPPP      E:::::::::::::::E     L:::::L                 I::::I  N::::::N  N::::N:::::::N  E:::::::::::::::E   
		#  P::::P              I::::I    P::::P              E::::::EEEEEEEEEE     L:::::L                 I::::I  N::::::N   N:::::::::::N  E::::::EEEEEEEEEE   
		#  P::::P              I::::I    P::::P              E:::::E               L:::::L                 I::::I  N::::::N    N::::::::::N  E:::::E             
		#  P::::P              I::::I    P::::P              E:::::E       EEEEEE  L:::::L         LLLLLL  I::::I  N::::::N     N:::::::::N  E:::::E       EEEEEE
		# PP::::::PP          II::::::IIPP::::::PP          EE::::::EEEEEEEE:::::ELL:::::::LLLLLLLLL:::::LII::::::IIN::::::N      N::::::::NEE::::::EEEEEEEE:::::E
		# P::::::::P          I::::::::IP::::::::P          E::::::::::::::::::::EL::::::::::::::::::::::LI::::::::IN::::::N       N:::::::NE::::::::::::::::::::E
		# P::::::::P          I::::::::IP::::::::P          E::::::::::::::::::::EL::::::::::::::::::::::LI::::::::IN::::::N        N::::::NE::::::::::::::::::::E
		# PPPPPPPPPP          IIIIIIIIIIPPPPPPPPPP          EEEEEEEEEEEEEEEEEEEEEELLLLLLLLLLLLLLLLLLLLLLLLIIIIIIIIIINNNNNNNN         NNNNNNNEEEEEEEEEEEEEEEEEEEEEE
		#
		### Targets
	#
	@cat Makefile* | grep -E '^[a-zA-Z_-]+:.*?## .*$$' | sort | awk 'BEGIN {FS = ":.*?## "}; {printf "\033[36m%-30s\033[0m %s\n", $$1, $$2}'

.PHONY: create_stack
create_stack: ## creates the infrastructure using the cfn template
	make create_update_infrastructure ACTION=create

.PHONY: update_stack
update_stack: ## updates the infrastructure using the cfn template
	make create_update_infrastructure ACTION=update

.PHONY: validate_template
validate_template: ## validates the yaml file
	aws cloudformation validate-template --template-body file://masterstack.yml

.PHONY: create_update_infrastructure
create_update_infrastructure: ## Creates or updates the infrastructure using the cfn template
	aws cloudformation ${ACTION}-stack \
		--stack-name $(PREFIX)-$(ENVIRONMENT)-master \
		--template-body file://release/masterstack_release.yaml \
		--profile $(PROFILE) \
		--capabilities CAPABILITY_AUTO_EXPAND \
		--region "eu-west-2" \
		--parameters \
			ParameterKey=Prefix,ParameterValue=$(PREFIX) \
			ParameterKey=Environment,ParameterValue=$(ENVIRONMENT) \
			ParameterKey=KeyName,ParameterValue=$(KEYNAME)


.PHONY: clean
clean: ## cleans the release files
	cd release && rm -rf *.*

.PHONY: release_changes
release_changes: ## releases the changes
	make clean
	make create_release_file
	make copy_s3_release_files

.PHONY: create_release_file
create_release_file: ## creates a localfile for deployment
	aws cloudformation package \
		--template-file vpcstack.yml \
		--s3-bucket "${ARTIFACTBUCKETNAME}" \
		--output-template-file release/vpcstack_release.yaml

	aws cloudformation package \
		--template-file loadbalancer.yml \
		--s3-bucket "${ARTIFACTBUCKETNAME}" \
		--output-template-file release/loadbalancer_release.yaml

	aws cloudformation package \
		--template-file apistack.yml \
		--s3-bucket "${ARTIFACTBUCKETNAME}" \
		--output-template-file release/apistack_release.yaml

	aws cloudformation package \
    --template-file masterstack.yml \
    --s3-bucket "${ARTIFACTBUCKETNAME}" \
    --output-template-file release/masterstack_release.yaml

.PHONY: copy_s3_release_files
copy_s3_release_files: ## copies release files to s3
	aws s3 cp release/vpcstack_release.yaml s3://nirooptestbucket/release/vpcstack_release.yaml
	aws s3 cp release/loadbalancer_release.yaml s3://nirooptestbucket/release/loadbalancer_release.yaml
	aws s3 cp release/apistack_release.yaml s3://nirooptestbucket/release/apistack_release.yaml




