SPEC=

RUN=nvim --headless --noplugin -u spec.vim

.PHONY: all nvim test watch prepare

prepare:
	git submodule update --depth 1 --init
	luarocks install luacheck --local

nvim:
	@nvim --noplugin -u spec.vim

test:
ifeq ($(strip $(SPEC)),) # a.k.a. $(SPEC) is empty
	@$(RUN) -c "PlenaryBustedDirectory spec/ { minimal_init = 'spec.vim' }"
else
	@$(RUN) -c "PlenaryBustedFile $(SPEC)"
endif

watch:
	@echo -e '\nRunning tests on "spec/**/*_spec.lua" when any Lua file on "../lua/" and "spec/" changes\n'
	@find spec/ ../lua/ -name '*.lua' \
	  | entr make test SPEC=$(SPEC)

rename:
	@if [ "$(words $(MAKECMDGOALS))" -ne 3 ]; then \
		echo "Error: Incorrect number of arguments. Usage: make rename current_plugin_name new_plugin_name"; \
		exit 1; \
	fi;
	$(eval CURRENT_NAME=$(word 2,$(MAKECMDGOALS)))
	$(eval NEW_NAME=$(word 3,$(MAKECMDGOALS)))
	@echo "Renaming all references from '$(CURRENT_NAME)' to $(NEW_NAME)"
	# Set LC_ALL to C for compatibility
	LC_ALL=C; \
	# Rename in file contents, excluding the Makefile itself
	for file in $$(find ../ -type f ! -path '../.git/*' ! -path './Makefile' -exec grep -Il '$(CURRENT_NAME)' {} \;); do \
		sed -i "" "s/$(CURRENT_NAME)/$(NEW_NAME)/g" "$$file"; \
	done
	# Rename files, excluding the Makefile
	for file in $$(find ../ -type f -name '*$(CURRENT_NAME)*' ! -path '../.git/*' ! -path './Makefile'); do \
		mv "$$file" "$$(echo $$file | sed 's/$(CURRENT_NAME)/$(NEW_NAME)/')"; \
	done
	# Rename directories, excluding .git and the Makefile's directory
	for dir in $$(find ../ -type d -name '*$(CURRENT_NAME)*' ! -path '../.git/*' ! -path './'); do \
		mv "$$dir" "$$(echo $$dir | sed 's/$(CURRENT_NAME)/$(NEW_NAME)/')"; \
	done
	%:
	@:

all: prepare test
