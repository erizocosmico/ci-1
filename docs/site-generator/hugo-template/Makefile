# Default shell
SHELL := /bin/bash

# Config
DESTINATION ?= public
LANDING_PATH ?=

# System
url_os = 64bit
os = amd64
ifeq ($(os),Windows_NT)
    arch = windows
    url_arch = Windows
else
    uname_s := $(shell uname -s)
    ifeq ($(uname_s),Linux)
			arch = linux
			url_arch = Linux
    endif
    ifeq ($(uname_s),Darwin)
			arch = darwin
			url_arch = MacOS
    endif
endif

# Hugo variables
hugo_version := 0.21
hugo_path := $(shell pwd)/.hugo
hugo_url = github.com/spf13/hugo
hugo_url_name := hugo_$(hugo_version)_$(url_arch)-$(url_os)

# Tools
curl = curl -L
hugo = $(hugo_path)/hugo
mkdir = mkdir -p
npm = npm
untar = tar -xf
rename = mv
remove = rm -rf

## Lists all recipes
list:
	@grep '^##' Makefile -A 1

## Install dependencies
dependencies: hugo-dependencies import-commons npm-dependencies

## Builds hugo project compiling assets and hugo files
hugo-build:
	$(npm) run build
	$(hugo) --config=hugo.config.yaml --destination=$(DESTINATION)

# Updates hugo dependencies
hugo-dependencies:
	@if [[ ! -f $(hugo) ]]; then \
		$(mkdir) $(hugo_path); \
		cd $(hugo_path); \
		ext="zip"; \
		if [ "$(arch)" == "linux" ]; then ext="tar.gz"; fi; \
		file="hugo.$${ext}"; \
		$(curl) https://$(hugo_url)/releases/download/v$(hugo_version)/$(hugo_url_name).$${ext} -o $${file}; \
		if [ "$(arch)" == "linux" ]; then tar -xvzf $${file}; else unzip $${file}; fi; \
	fi;

export_filename = $(shell pwd)/landing-common.tar
file_list_name = .list.gitignore
# Imports common stuff
import-commons:
	$(MAKE) -C $(LANDING_PATH) export-landing-commons \
		target=$(export_filename) \
		file_list_name=$(file_list_name)
	$(untar) $(export_filename)
	$(rename) $(file_list_name) .gitignore
	$(remove) $(export_filename)

# Prepares npm
npm-dependencies:
	$(npm) install
