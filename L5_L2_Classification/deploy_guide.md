# Deploy Guide — MF_SO_PASSWORD_MANAGER
## Prerequisites
- Python 3.11+, cryptography 42.0+, argon2-cffi 23.0+, SQLite (stdlib)

## Environment
- CPU-only. 256MB RAM. AES-256-GCM via OpenSSL (hardware acceleration automatic).

## Install
```bash
pip install anticloud-mfso cryptography argon2-cffi
```

## Create vault
```bash
python -m mf_so_password_manager create --output ./anticloud.vault
# Prompts for master password. Argon2id derivation: time=3, mem=64MB.
```

## Air-Gap
Single encrypted SQLite file. No network required at any point.

## AIOSS Integration
```bash
aioss init --module MF_SO_PASSWORD_MANAGER --output ./vault_audit.aioss
```
All secret access auto-appended.

## Verification
```bash
python -m mf_so_password_manager verify --vault ./anticloud.vault
```
