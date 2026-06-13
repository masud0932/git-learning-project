# Security Policy

## Reporting a Vulnerability

If you discover a security vulnerability, credential exposure, or sensitive information leak in this repository, please report it responsibly.

Examples include:

* Exposed passwords, API keys, or tokens
* Secrets committed to Git history
* Insecure CI/CD configurations
* Vulnerable dependencies
* Misconfigured Kubernetes or cloud resources
* Authentication or authorization issues

Please avoid creating public GitHub issues for sensitive vulnerabilities.

---

# Secret & Credential Exposure

This repository follows security best practices to prevent accidental credential leakage through source control.

## Important Notice

Sensitive information should never be committed to Git, including:

* `.env` files
* AWS access keys
* Database passwords
* SSH private keys
* Kubernetes secrets
* API tokens
* Terraform secret variables
* CI/CD credentials

Even if secrets are later deleted from files, they may still remain accessible in Git history.

---

# Examples of Unsafe Practices

## Example 1 — Hardcoded Database Password

❌ Unsafe:

```env id="2rj1nq"
DB_PASSWORD=mysecretpassword123
```

✅ Recommended:

```env id="7fjlwm"
DB_PASSWORD=${DB_PASSWORD}
```

Store the real value in:

* GitHub Secrets
* AWS Secrets Manager
* Kubernetes Secrets

---

## Example 2 — AWS Credentials in Source Code

❌ Unsafe:

```python id="9q5zgm"
AWS_ACCESS_KEY_ID=AKIAxxxxxxxx
AWS_SECRET_ACCESS_KEY=abcd123456
```

✅ Recommended:

Use IAM Roles, IRSA, or environment variables instead.

---

## Example 3 — Kubernetes Secret Inside Git

❌ Unsafe:

```yaml id="jkhg2s"
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: bXlzZWNyZXQ=
```

Even base64-encoded values are not secure.

✅ Recommended:

Use:

* External Secrets Operator
* AWS Secrets Manager
* Vault

---

## Example 4 — Terraform Secrets

❌ Unsafe:

```hcl id="qj2h4y"
db_password = "mypassword123"
```

inside:

```text id="zby4gm"
terraform.tfvars
```

✅ Recommended:

Use:

* environment variables
* AWS Secrets Manager
* encrypted secret storage

---

# If a Secret Is Accidentally Pushed

The following incident response procedure should be followed immediately.

---

## 1. Rotate or Revoke the Secret

Examples:

| Leaked Secret     | Immediate Action                     |
| ----------------- | ------------------------------------ |
| AWS Access Key    | Deactivate and create new key        |
| Database Password | Change password                      |
| GitHub Token      | Revoke token                         |
| SSH Private Key   | Regenerate key pair                  |
| Kubernetes Secret | Replace secret and restart workloads |

Removing secrets from code alone is not sufficient.

---

## 2. Remove Sensitive Data from Source Code

Replace hardcoded credentials with secure secret management solutions such as:

* GitHub Secrets
* AWS Secrets Manager
* Kubernetes Secrets
* HashiCorp Vault
* Environment Variables
* External Secrets Operator

---

## 3. Clean Git History

If sensitive data exists in commit history, repository history should be rewritten.

### Example Using git-filter-repo

Remove `.env` file from all commits:

```bash id="5k0s5z"
git filter-repo --path .env --invert-paths
```

Force push cleaned history:

```bash id="93hz7n"
git push origin --force --all
git push origin --force --tags
```

---

# Secret Scanning Examples

## Using Gitleaks

```bash id="n4n4px"
gitleaks detect
```

## Using TruffleHog

```bash id="ngjlwm"
trufflehog git file://.
```

---

# Recommended .gitignore Entries

```gitignore id="czfdo1"
.env
*.pem
*.key
terraform.tfvars
secrets/
*.crt
```

---

# CI/CD Security Recommendations

## GitHub Actions Example

✅ Recommended:

```yaml id="okz8wx"
env:
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

❌ Unsafe:

```yaml id="8tnzcn"
env:
  DB_PASSWORD: mypassword123
```

---

# Security Best Practices

This project recommends:

* Using least-privilege IAM permissions
* Enabling secret scanning tools
* Rotating credentials regularly
* Avoiding secrets in logs
* Using encrypted secret management systems
* Reviewing pull requests for sensitive data
* Using Kubernetes RBAC and IRSA where applicable

---

# Disclaimer

All contributors are responsible for ensuring that sensitive credentials and confidential information are not committed to this repository.
