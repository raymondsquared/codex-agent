# Shell Script Coding Standards

- Set `set -euo pipefail` for strict error handling.
- Always validate input arguments at the start of scripts and functions.
- Use `#!/bin/bash` or `#!/bin/sh` as the first line (shebang).
- Quote variables: "$var" instead of $var.
- Check command exit codes and handle errors.
- Log or print errors to stderr.
- Use functions for reusable logic.
- Avoid hardcoding paths; use variables.
- Use comments to explain complex logic.
- Document script usage and parameters at the top with a comment block.
- Use consistent indentation with 2 spaces.
- Prefer `$(...)` for command substitution.

### Example Template

```bash
#!/bin/bash

# Usage: ./script.sh <name>
# Description: Greets the user with their name in uppercase.

set -euo pipefail

if [[ $# -ne 1 ]]; then
  echo "Usage: $0 <name>" >&2
  exit 1
fi

name="$1"
upper_name="$(echo "$name" | tr '[:lower:]' '[:upper:]')"
echo "Hello, $upper_name!"
```
