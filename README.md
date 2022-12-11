# Building a distributed cli using Makefile

This repository how we can distribute and update common makefile targets to multiple repositories.

## Getting started

1. Clone the repository <https://github.com/fbeltrao/using-mlops-cli>. This is an example repository using the cli.
1. Run `make init`. See that the folder `.mlops` will be available locally but not source controlled.
1. Run `make train` which uses common targets defined in this cli (not the repository makefile).

## How it works

Makefile can include other files to extend the available targets. They can be declared optional by using `-include <file_to_include>`. The final list of target is build from the combination of all makefiles.
The implementation provides a starting makefile to be used in code repositories. It contains an `init` target that download common targets from this repository.

The starting makefile for code repositories is (see [example](https://github.com/fbeltrao/using-mlops-cli)):

```makefile
-include .mlops/makefile
MLOPS_REPO_URL=https://github.com/fbeltrao/mlops-cli.git
MLOPS_REPO_VERSION=main
include config.env

##################################################################
## Project specific targets
##################################################################
train: init mlops-train

##################################################################
## ML Ops targets (DO NOT MODIFY)
##################################################################
.PHONY: init
updateTools=false
init:
ifneq ($(updateTools),true)
ifneq ($(wildcard .mlops/makefile),)
init:
else
init: -setup-mlops
endif
else
init: -setup-mlops
endif

.PHONY: -setup-mlops
-setup-mlops:
	@echo -n "Running ML Ops setup..."
	@rm .mlops-temp -rf && rm .mlops -rf
	@git clone $(MLOPS_REPO_URL) --no-checkout --quiet --depth 1 .mlops-temp
	@cd .mlops-temp && git sparse-checkout init --cone && git sparse-checkout set .mlops && git checkout $(MLOPS_REPO_VERSION) --quiet
	@mkdir -p .mlops && cp -r .mlops-temp/.mlops . && rm .mlops-temp -rf && echo " Done! ✅"

```
