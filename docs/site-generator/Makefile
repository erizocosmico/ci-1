# Default shell
SHELL := /bin/sh

# Config
SHARED ?= /etc/shared
SOURCES ?=
OUTPUT ?=
export BASEURL ?=
export OWNER ?= src-d
export REPOSITORY ?=
export VERSION ?=
export LANGUAGES ?=

# Variables
landing_path := $(SHARED)/landing
site_sources_path := $(SOURCES)/.doc-site-sources
hugo_template_path = ./hugo-template
makefile_templates_paths = ./makefile-templates

# Tools
copy = cp -R
remove = rm -rf

## Lists all recipes
list:
	@grep '^##' Makefile -A 1

## Generates a documentation-site based in sourced-landing for the required project
docs: prepare-hugo site-build clean-up

# Prepares the hugo folder with the landing commons and the repo documentation
prepare-hugo: ensure-building-conf copy-sources extract-repo-documentation

# Copies the hugo template into the site sources folder
copy-sources:
	$(remove) $(site_sources_path)
	$(copy) $(hugo_template_path) $(site_sources_path)

hugo_content_raw_path = $(site_sources_path)/hugo/content/raw
project_yml_path = $(site_sources_path)/hugo/data
# Extracts the documentation contents from the project repository
extract-repo-documentation:
	$(copy) $(SOURCES)/README.md $(hugo_content_raw_path)

	$(copy) $(SOURCES)/LICENSE $(hugo_content_raw_path)

	@if [ -d $(SOURCES)/_examples ]; then \
		$(copy) $(SOURCES)/_examples/* $(hugo_content_raw_path)/examples/; \
	fi;

	@if [ -d $(SOURCES)/_tutorials ]; then \
		$(copy) $(SOURCES)/_tutorials/* $(hugo_content_raw_path)/tutorials/; \
	fi;

	@if [ -d $(SOURCES)/_downloads ]; then \
		$(copy) $(SOURCES)/_downloads/* $(hugo_content_raw_path)/downloads/; \
	fi;

	@envsubst < $(makefile_templates_paths)/project.yml > $(project_yml_path)/project.yml

# Builds the documentation-site
site-build:
	$(MAKE) -C $(site_sources_path) hugo-build DESTINATION=$(OUTPUT)

# Cleans up
clean-up:
	$(remove) $(site_sources_path)

# Ensures that configuration is provided
ensure-building-conf:
	@if [ -z "$(SOURCES)" ]; then \
		echo "**error 'SOURCES' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [ -z "$(OUTPUT)" ]; then \
		echo "**error 'OUTPUT' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [ -z "$(BASEURL)" ]; then \
		echo "**error 'BASEURL' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [ -z "$(REPOSITORY)" ]; then \
		echo "**error 'REPOSITORY' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [ -z "$(VERSION)" ]; then \
		echo "**error 'VERSION' is undefined. STOP"; \
		exit 1; \
	fi;
	@if [ -z "$(LANGUAGES)" ]; then \
		echo "**error 'LANGUAGES' is undefined. STOP"; \
		exit 1; \
	fi;
