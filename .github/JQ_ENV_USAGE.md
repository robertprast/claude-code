# Using jq with Environment Variables

This document explains how to use `jq` to access environment variables in GitHub Actions workflows.

## Important Note

The `GITHUB_COPILOT_API_TOKEN` referenced in this guide should be a dedicated GitHub Copilot API token stored in your repository secrets. If you don't have a specific Copilot API token, you can use `GITHUB_TOKEN` as a fallback for testing purposes, but be aware that it may not have the same permissions or functionality as a proper Copilot API token.

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
    # Use your GitHub Copilot API token secret
    GITHUB_COPILOT_API_TOKEN: ${{ secrets.GITHUB_COPILOT_API_TOKEN }}
  run: |
    # Access the token via jq (with fallback to empty string if not set)
    TOKEN=$(jq -n -r 'env.GITHUB_COPILOT_API_TOKEN // ""')
    echo "Token is set: $([ -n "$TOKEN" ] && echo "yes" || echo "no")"
```

**Note**: For testing purposes without a Copilot API token, you can temporarily use:
```yaml
GITHUB_COPILOT_API_TOKEN: ${{ secrets.GITHUB_TOKEN }}
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
