# Default shell
SHELL := /bin/bash

# Config
SHARED := $(shared)
export BASEURL := $(baseurl)
export OUTPUT := $(output)
export OWNER := $(owner)
export REPOSITORY := $(repository)
export VERSION := $(version)
export LANGUAGES := $(languages)

URL_TARBALL := https://github.com/$(OWNER)/$(REPOSITORY)/archive/$(VERSION).tar.gz
REPO_TMP_PATH := $(SHARED)/$(OWNER)-$(REPOSITORY)-$(VERSION)
SITE_TMP_PATH := $(SHARED)/$(OWNER)-$(REPOSITORY)-$(VERSION)-site
LANDING_RELATIVE_PATH := $(SHARED)/landing

# Variables
LANDING_REPOSITORY = https://github.com/src-d/landing.git
HUGO_TEMPLATE_PATH = ./hugo-template
MAKEFILE_TEMPLATE_PATH = ./makefile-templates

# Environment
BASE_PATH := $(shell pwd)

# Tools
CURL = curl -L
MKDIR = mkdir -p
COPY = cp -R
REMOVE = rm -rf
CLONE = git clone --quiet
UNTAR = tar -xf
UNTARGZ = tar -xzf

## Lists all recipes
list:
	@grep '^##' Makefile -A 1

## Generates a documentation-site based in sourced-landing for the required project
docs: prepare-hugo site-build clean-up

# Ensures that configuration is provided
ensure-conf: 
	@if [[ -z "$(SHARED)" ]]; then \
		echo "**error 'shared' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(BASEURL)" ]]; then \
		echo "**error 'baseurl' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(OUTPUT)" ]]; then \
		echo "**error 'output' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(OWNER)" ]]; then \
		echo "**error 'owner' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(REPOSITORY)" ]]; then \
		echo "**error 'repository' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(VERSION)" ]]; then \
		echo "**error 'version' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [[ -z "$(LANGUAGES)" ]]; then \
		echo "**error 'languages' is undefined. STOP"; \
		exit 1; \
	fi;

# Prepares the hugo folder with the landing commons and the repo documentation
prepare-hugo: ensure-conf copy-template checkout-landing import-commons checkout-repository extract-repo-documentation

# Copies the template into the site temporal folder
copy-template:
	@if [[ -d $(SITE_TMP_PATH) ]]; then \
		if [[ ! -f $(SITE_TMP_PATH)/static/.gitkeep ]]; then echo "$(SITE_TMP_PATH) folder already contains non documentation-site data"; exit 1; fi; \
		$(REMOVE) $(SITE_TMP_PATH); \
		echo "Restoring $(SITE_TMP_PATH)"; \
	fi;
	$(MKDIR) $(SITE_TMP_PATH);
	$(COPY) $(HUGO_TEMPLATE_PATH)/* $(SITE_TMP_PATH)

# Downloads landing
checkout-landing:
	@if [[ ! -d $(LANDING_RELATIVE_PATH) ]]; then \
		$(CLONE) $(LANDING_REPOSITORY) $(LANDING_RELATIVE_PATH); \
	fi;

export_filename = $(SHARED)/landing-common.tar
# Imports common stuff
import-commons:
	$(MAKE) -C $(LANDING_RELATIVE_PATH) export-landing-commons target=$(export_filename)
	$(UNTAR) $(export_filename) --directory $(SITE_TMP_PATH)
	$(REMOVE) $(export_filename)

repository_filename = repo.tar.gz
# Downloads the repository whose documentation-site must be generated
checkout-repository:
	$(MKDIR) $(REPO_TMP_PATH); \
	cd $(REPO_TMP_PATH); \
	$(CURL) $(URL_TARBALL) -o $(repository_filename); \
	$(UNTARGZ) $(repository_filename) --strip 1;
	$(REMOVE) $(repository_filename);

hugo_content_raw_path = $(SITE_TMP_PATH)/hugo/content/raw
project_yml_output = $(SITE_TMP_PATH)/hugo/data/project.yml
caddyfile_output = $(OUTPUT)/Caddyfile
# Extracts the documentation contents from the project repository
extract-repo-documentation:
	$(COPY) $(REPO_TMP_PATH)/README.md $(hugo_content_raw_path)

	$(COPY) $(REPO_TMP_PATH)/LICENSE $(hugo_content_raw_path)

	@if [[ -d $(REPO_TMP_PATH)/_examples ]]; then \
		$(COPY) $(REPO_TMP_PATH)/_examples/* $(hugo_content_raw_path)/examples/; \
	fi;

	@if [[ -d $(REPO_TMP_PATH)/_tutorials ]]; then \
		$(COPY) $(REPO_TMP_PATH)/_tutorials/* $(hugo_content_raw_path)/tutorials/; \
	fi;

	@if [[ -d $(REPO_TMP_PATH)/_downloads ]]; then \
		$(COPY) $(REPO_TMP_PATH)/_downloads/* $(hugo_content_raw_path)/downloads/; \
	fi;

	@envsubst < $(MAKEFILE_TEMPLATE_PATH)/Caddyfile > $(caddyfile_output)

	@envsubst < $(MAKEFILE_TEMPLATE_PATH)/project.yml > $(project_yml_output)

	@if [ ! -z "$(LANGUAGES)" ]; then \
		docUrls="docUrls:\n"; \
		for lang in $(LANGUAGES); do \
			docUrls+="  - lang: $$lang\n"; \
			if [ "$$lang" == "go" ]; then \
				docUrls+="    url: https://godoc.org/github.com/$(OWNER)/$(REPOSITORY)"; \
			else \
				docUrls+="    url: $(BASE_URL)/api/$$lang/"; \
			fi; \
			docUrls+="\n"; \
		done; \
		echo -e "$$docUrls" >> $(project_yml_output); \
	fi;

# Builds the documentation-site
site-build:
	$(MAKE) -C $(SITE_TMP_PATH) hugo-build
	$(COPY) $(SITE_TMP_PATH)/public $(OUTPUT)

# Cleans up
clean-up:
	$(REMOVE) $(REPO_TMP_PATH)
	$(REMOVE) $(SITE_TMP_PATH)
