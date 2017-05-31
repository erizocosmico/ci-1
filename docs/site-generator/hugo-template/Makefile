# Default shell
SHELL := /bin/bash

# Config
HUGO_VERSION := 0.21
LANDING_RELATIVE_PATH :=  ../landing
LANDING_COMMONS_TAR := landing-common.tar

# System
URL_OS = 64bit
OS = amd64
ifeq ($(OS),Windows_NT)
    ARCH = windows
    URL_ARCH = Windows
else
    UNAME_S := $(shell uname -s)
    ifeq ($(UNAME_S),Linux)
			ARCH = linux
			URL_ARCH = Linux
    endif
    ifeq ($(UNAME_S),Darwin)
			ARCH = darwin
			URL_ARCH = MacOS
    endif
endif

# Environment
BASE_PATH := $(shell pwd)
HUGO_PATH := $(BASE_PATH)/.hugo
HUGO_URL = github.com/spf13/hugo
HUGO_NAME := hugo
HUGO_URL_NAME := hugo_$(HUGO_VERSION)_$(URL_ARCH)-$(URL_OS)

# Tools
CURL = curl -L
HUGO = $(HUGO_PATH)/$(HUGO_NAME)
MKDIR = mkdir -p
NPM = npm

## Lists all recipes
list:
	@grep '^##' Makefile -A 1

# Updates hugo dependencies
hugo-dependencies:
	@if [[ ! -f $(HUGO) ]]; then \
		$(MKDIR) $(HUGO_PATH); \
		cd $(HUGO_PATH); \
		ext="zip"; \
		if [ "$(ARCH)" == "linux" ]; then ext="tar.gz"; fi; \
		file="hugo.$${ext}"; \
		$(CURL) https://$(HUGO_URL)/releases/download/v$(HUGO_VERSION)/$(HUGO_URL_NAME).$${ext} -o $${file}; \
		if [ "$(ARCH)" == "linux" ]; then tar -xvzf $${file}; else unzip $${file}; fi; \
	fi;

# Prepares npm
npm-dependencies:
	$(NPM) install
	$(NPM) run build

## Builds hugo project
hugo-build: npm-dependencies hugo-dependencies
	$(HUGO) --config=hugo.config.yaml --destination=public

# Runs hugo server
hugo-server:
	$(HUGO) server --config=hugo.config.yaml --destination=public --port=8585 --watch --buildDrafts -b http://tacoshrimp.com

## Cleans and run a new hugo server with webpack watcher enabled
landing-local-serve: hugo-clean npm-dependencies hugo-dependencies
	$(NPM) run serve

# Deletes the hugo folder
hugo-clean:
	rm -rf $(HUGO_PATH)
