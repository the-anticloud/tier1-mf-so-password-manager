# MF_SO_PASSWORD_MANAGER — Student Getting Started

## What You'll Build
A local-only, AI-enhanced password manager that audits your vault for weak/reused passwords, generates strong alternatives, and never sends your credentials anywhere.

## Prerequisites
- Python 3.10+
- Ollama with llama3:8b (for AI audit features)
- No cloud accounts required

## Install
```bash
pip install mfso-password-manager
ollama pull llama3:8b
```

## First Working Example
```python
from mfso import Vault, MasterKey
import secrets

# Create a new local vault
master_password = input("Enter your master password: ")
key = MasterKey.derive(master_password, algorithm="argon2id")
vault = Vault.create("./my_vault.enc", key)

# Add a few entries
vault.add_entry(site="github.com", username="me@email.com",
                password=secrets.token_urlsafe(20))
vault.add_entry(site="example.com", username="me@email.com",
                password="weak_password_123")  # intentionally weak for demo
vault.save()
print(f"Vault created with {len(vault.entries)} entries")
```

## Run an AI Audit
```python
from mfso import Vault, MasterKey, PasswordAuditor, LocalLLMAdvisor

key = MasterKey.derive("your_master_password", algorithm="argon2id")
vault = Vault.load("./my_vault.enc", key)

auditor = PasswordAuditor()
report = auditor.audit(vault)
for issue in report.issues:
    print(f"[{issue.severity}] {issue.site}: {issue.description}")

advisor = LocalLLMAdvisor(model="ollama/llama3:8b")
suggestion = advisor.suggest_password(site="github.com", length=24)
print(f"Suggested strong password: {suggestion}")
```

## On Kaggle (loiskleinner account, T4 GPU)
```python
!pip install mfso-password-manager
# Note: on Kaggle use a dummy vault — never put real passwords in a notebook
```

## What's Next
- Explore the AI audit report categories: reuse, weakness, breach exposure
- Try generating a passphrase vs. random password for memorability
- Read the EDUCATORS guide for cryptographic details
