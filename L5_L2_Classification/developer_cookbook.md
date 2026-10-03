# Developer Cookbook — MF_SO_PASSWORD_MANAGER
**Stack:** Python 3.11, AES-256-GCM, Argon2id, SQLite

## Initialize vault
```python
from mf_so_password_manager import SecretVault
vault = SecretVault.create("./anticloud.vault", master_password="<strong-password>")
```

## Store secrets
```python
vault.store("pax_model_key", b"<32-byte-aes-key>", metadata={"purpose": "PAX 27B weights"})
vault.store("aioss_signing_key", b"<ed25519-key>", metadata={"purpose": "AIOSS chain signing"})
```

## Retrieve (AIOSS-audited)
```python
key = vault.retrieve("pax_model_key", accessor="PAX_INFERENCE_CORE",
                     aioss_chain="./vault_audit.aioss")
# key zeroed from memory after use
```

## Rotate a secret
```python
vault.rotate("aioss_signing_key", new_value=new_key_bytes)
```

## Access audit log
```python
for entry in vault.access_log(secret="pax_model_key"):
    print(f"{entry.timestamp} — {entry.accessor}")
```

## Performance
Argon2id: time_cost=3, memory_cost=65536 — <500ms unlock, brute-force prohibitive.
Cache derived key in mlock'd page; zero on shutdown.

## Integration
Called by PAX_INFERENCE_CORE (T2), AIOSS_FORMAT, LIBERN_PLATFORM, SOVEREIGN_OS at boot.
