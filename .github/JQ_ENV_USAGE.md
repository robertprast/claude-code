# Using jq with Environment Variables

This document explains how to use `jq` to access environment variables in GitHub Actions workflows.

## Basic Usage

The `jq` command-line JSON processor can access environment variables using the `env` object:

```bash
# Access a specific environment variable
jq -n 'env.VARIABLE_NAME'

# List all environment variables
jq -n 'env'

# Check if a variable exists
jq -n 'env.VARIABLE_NAME != null'
```

## Example: Accessing GITHUB_COPILOT_API_TOKEN

To access the `GITHUB_COPILOT_API_TOKEN` environment variable:

```bash
# In a GitHub Actions workflow
- name: Use Copilot API Token
  env:
    GITHUB_COPILOT_API_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  run: |
    # Access the token via jq
    TOKEN=$(jq -n -r 'env.GITHUB_COPILOT_API_TOKEN')
    echo "Token is set: $([ -n "$TOKEN" ] && echo "yes" || echo "no")"
```

## Common Patterns

### Extract multiple environment variables as JSON

```bash
jq -n '{
  workspace: env.GITHUB_WORKSPACE,
  ref: env.GITHUB_REF,
  token: env.GITHUB_COPILOT_API_TOKEN
}'
```

### Filter environment variables by prefix

```bash
jq -n 'env | with_entries(select(.key | startswith("GITHUB_")))'
```

### Use in workflow conditions

```bash
if jq -n 'env.GITHUB_COPILOT_API_TOKEN != null' | grep -q true; then
  echo "Token is available"
fi
```

## Security Notes

- Always be careful when logging or displaying tokens
- Use `-r` flag for raw output without quotes when extracting secrets
- Never commit secrets or tokens to the repository
- Use GitHub Actions secrets to pass sensitive values

## See Also

- [jq Manual](https://jqlang.github.io/jq/manual/)
- [GitHub Actions Environment Variables](https://docs.github.com/en/actions/learn-github-actions/variables)
- [GitHub Actions Secrets](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)
