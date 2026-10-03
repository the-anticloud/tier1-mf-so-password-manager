# MF_SO_PASSWORD_MANAGER — Educator's Teaching Guide

## Course Fit: cybersecurity, cryptography, secure software engineering, AI-assisted security

## 3-Week Module: AI-Enhanced Sovereign Password Management

### Week 1: Password Security Fundamentals
**Lecture Topics:**
- Why password managers matter: credential stuffing, breach databases, reuse attacks
- MF+SO architecture: multi-factor + sovereign (local-only) operation
- Cryptographic primitives: AES-256-GCM, Argon2id, PBKDF2
- Threat modeling a password manager: what can go wrong

**Lab Exercise:**
```python
from mfso import Vault, MasterKey
import secrets
# Create a local vault with strong key derivation
master_password = "correct-horse-battery-staple"
key = MasterKey.derive(master_password, algorithm="argon2id",
                        time_cost=3, memory_cost=65536)
vault = Vault.create("./my_vault.enc", key)
vault.add_entry(site="example.com", username="alice", password=secrets.token_urlsafe(20))
vault.save()
print(f"Vault entries: {len(vault.entries)}")
```

### Week 2: AI Features — Breach Detection and Password Strength Analysis
**Lecture Topics:**
- AI-assisted password auditing without cloud calls
- Local breach detection: using Have I Been Pwned datasets offline
- Language model analysis of password patterns (local only)
- Generating secure, memorable passwords with local LLMs

**Lab Exercise:**
```python
from mfso import PasswordAuditor, LocalLLMAdvisor
auditor = PasswordAuditor(breach_db="./hibp_sha1.db")  # local HIBP dataset
vault = Vault.load("./my_vault.enc", key)
report = auditor.audit(vault)
for issue in report.issues:
    print(f"[{issue.severity}] {issue.site}: {issue.description}")
advisor = LocalLLMAdvisor(model="ollama/llama3:8b")
suggestion = advisor.suggest_password(site="banking_portal", length=20)
print(f"Suggested password: {suggestion}")
```

### Week 3: Integration with SOVEREIGN_OS and Anticloud
**Lecture Topics:**
- MF_SO_PASSWORD_MANAGER as a SOVEREIGN_OS service
- AIOSS format for access audit logs (no plaintext credentials in ledger)
- Zero-knowledge sync between devices (local network only)
- Emergency access and recovery key architecture

**Lab Exercise:**
```python
from mfso import Vault, SyncServer
server = SyncServer(bind="127.0.0.1:9999", auth="local-cert")
vault1 = Vault.load("./device1_vault.enc", key)
vault2 = Vault.sync_from(server, device_id="device-2", key=key)
print(f"Synced {len(vault2.entries)} entries from device 1")
```

## Exam Questions
1. Compare Argon2id and PBKDF2 as master password KDF algorithms. Why is Argon2id preferred for new deployments?
2. Explain how a zero-knowledge sync works. What does the sync server learn about the vault contents during synchronization?
3. Why should AI-assisted password analysis run locally rather than via a cloud API? What data leakage risks does cloud analysis introduce?
