# Makefile Standards

- Always provide a default target (e.g., `all`).
- Always include a `help` target that documents available commands.
- Use `.DEFAULT_GOAL := help` to make help the default target.
- Use `.PHONY` for non file targets.
- Indent recipes with a tab (not spaces).
- Use consistent variable naming (UPPERCASE by convention).
- Clearly declare dependencies for each target.
- Quote variables in shell commands to handle spaces safely.
- Avoid trailing whitespace.
- Provide targets to clean up intermediate files as well as build artefacts.

## Example Template

```makefile
.PHONY: all build clean help

SHELL := /bin/bash
SRC := src/

.DEFAULT_GOAL := help

help:
	@echo "Available targets:"
	@echo "  all     - Build the project (default)"
	@echo "  build   - Build the project"
	@echo "  clean   - Clean up build artefacts"
	@echo "  help    - Show this help message"

all: build

build:
	@echo "Building project..."

clean:
	@echo "Cleaning up..."
```
