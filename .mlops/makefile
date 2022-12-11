mlops-train: jobfile=$(PIPELINE_FILE)
.PHONY: mlops-train
register_model=false
mlops-train: init
	@echo training from $(jobfile), register model=$(register_model)...
	@cat $(jobfile)
	@echo "\nDone!"

mlops-setup-tools:
	@echo installing azcli...