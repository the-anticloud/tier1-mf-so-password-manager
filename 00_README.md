# MF+SO — Sovereign Identity & Authentication Vault

**Status:** Production-ready | **Version:** 1.0.0 | **Author:** Lois-Kleinner Alpasan

---

## What Is MF+SO?

MF+SO is a local-first, zero-knowledge identity provider that turns your mobile device into a FIDO2 hardware key. It backs up via cryptographic seed phrases (BIP39), syncs via encrypted P2P (no central servers), and natively masks your identity. It replaces your password manager, your authenticator app, and your physical wallet.

MF+SO stands alone but is strengthened by integration with K5 (password hashing), Libern (P2P sync), and AIOSS (audit trail).

---

## Core Paradigms: Secure by Math, Not Trust

### 1. Passwordless by Default (Phone as Hardware Key)

MF+SO implements WebAuthn / FIDO2 protocols natively. Instead of typing passwords, MF+SO uses the Secure Enclave (iOS) or Titan/TrustZone (Android) to generate asymmetric keypairs (Ed25519 or P-256) for every website.

**UX:** Click "Login" → Phone buzzes → Scan fingerprint/FaceID → Logged in.

**Why it beats competitors:**
- Passkeys cannot be phished
- If tricked into visiting `paypa1.com` (typo domain), the TLS certificate mismatch is detected and signing is refused
- No 6-digit codes to intercept or steal

### 2. Root Identity (BIP39 Seed Phrases)

Instead of email + password (brute-forceable), MF+SO generates a 24-word BIP39 seed phrase.

This seed phrase derives the master root key that encrypts your entire vault.

**Export & Backup:**
- Users write it down, or
- Download heavily encrypted recovery PDF
- Ownership stays with user (not cloud provider)

### 3. Shamir's Secret Sharing (Social Recovery)

Losing a 24-word seed = total data loss. MF+SO implements Shamir's Secret Sharing:

- Seed phrase mathematically split into 5 shards
- Give 1 shard to spouse, 1 to lawyer, 1 to safe, etc.
- Any 3 of 5 shards reconstruct the master seed
- Recovery doesn't require central service

---

## Key Features

### Authentication
- **FIDO2/WebAuthn** — Passwordless login (passkeys)
- **TOTP support** — Import/export authenticator codes
- **U2F security keys** — Hardware token support
- **Biometric verification** — Fingerprint, FaceID on mobile
- **Multi-device sync** — Encrypted P2P sync via Libern

### Credential Management
- **Password vault** — Encrypted storage with auto-fill
- **Custom schemas** — API keys, license codes, router credentials
- **Recovery codes** — Store 2FA backup codes (GitHub, etc.)
- **Identity masking** — Ephemeral emails for sign-ups
- **Iconic recognition** — Auto-generated SVG icons for each service

### Identity
- **BIP39 seed phrase** — 24-word recovery key
- **Shamir shards** — Split recovery across trusted people
- **DID support** — Decentralized identifier option
- **Ed25519 keys** — Cryptographic identity signing
- **AIOSS integration** — Sign all identity operations

### Privacy
- **Zero-knowledge** — Server can't see master password (local key derivation)
- **End-to-end encryption** — All vault data encrypted before network
- **No telemetry** — Zero analytics collected
- **Local-first** — Works offline completely
- **P2P sync** — No central cloud database

---

## Quick Start

```bash
# First launch (setup)
mfso init

# You'll be prompted:
# 1. Create fingerprint/FaceID unlock
# 2. Generate 24-word BIP39 seed phrase
# 3. Set recovery options (Shamir shards or PDF)
# 4. Confirm master passphrase

# Add a website/service
mfso add facebook.com

# Prompted:
# 1. Scan fingerprint/FaceID
# 2. MF+SO generates passkey
# 3. Browser redirects to sign-in

# CLI for advanced users
mfso list
mfso search "social media"
mfso export --format encrypted-pdf
mfso backup --shamir 5 --threshold 3
```

---

## Architecture

```
┌──────────────────────────────────────┐
│  Desktop/Mobile UI                   │
│  (React Native + SwiftUI + Compose)  │
└──────────┬───────────────────────────┘
           │
    ┌──────▼─────────────────────┐
    │ Biometric Unlock           │
    │ (Secure Enclave / TrustZone)│
    └──────┬─────────────────────┘
           │
    ┌──────▼─────────────────────┐
    │ Master Key Derivation      │
    │ (BIP39 seed → PBKDF2)      │
    └──────┬─────────────────────┘
           │
    ┌──────▼─────────────────────────────────────┐
    │            Vault Engine                    │
    │ ┌────────────────────────────────────────┐ │
    │ │  FIDO2/WebAuthn (passkeys)            │ │
    │ │  TOTP (authenticator codes)           │ │
    │ │  Password storage (encrypted)         │ │
    │ │  Custom fields (API keys, etc.)       │ │
    │ └────────────────────────────────────────┘ │
    └──────┬─────────────────────────────────────┘
           │
    ┌──────▼──────────────┐
    │ Encryption Layer    │
    │ XChaCha20-Poly1305  │
    └──────┬──────────────┘
           │
    ┌──────▼──────────────┐
    │ Persistence Layer   │
    │ Secure Enclave DB   │
    └──────┬──────────────┘
           │
    ┌──────▼──────────────┐
    │ P2P Sync (Libern)   │
    │ (optional)          │
    └──────────────────────┘
```

---

## Use Cases

### 1. Enterprise Passwordless Identity
**Problem:** Okta costs $100+/user/year; centralized honeypot

**Solution:** Deploy MF+SO corporate vault
- Passkeys instead of passwords
- P2P sync across devices (via Libern)
- Zero cost (self-hosted)
- Employees control their own identities

### 2. Personal Credential Vault
**Problem:** Password reuse, weak passwords, 2FA code management

**Solution:** MF+SO as single vault
- Unique passkey per service (unphishable)
- 24-word seed for recovery
- One unlock per day
- AIOSS audit of all vault operations

### 3. Social Recovery from Device Loss
**Problem:** Phone lost/stolen = total access loss

**Solution:** Shamir's Secret Sharing
- Split 24-word seed into 5 shards
- Give shards to trusted people
- Recover by collecting any 3 shards
- No central recovery service needed

### 4. Ephemeral Identity for Privacy
**Problem:** Services track you via email

**Solution:** MF+SO identity masking
- Generate unique email per signup: `service-random-id@kathon.id`
- Emails forwarded to real email
- Terminate alias if service gets breached
- Service never learns your real identity

---

## Security

### Threat Model

| Threat | Defense |
|--------|---------|
| **Phishing** | FIDO2 refuses to sign mismatched TLS cert |
| **Brute force** | Biometric lock + rate limiting |
| **Password theft** | Passkeys used instead of passwords |
| **Device loss** | Shamir shards can recover seed |
| **Supply chain** | No cloud backend to compromise |
| **Quantum attacks** | K5-H hardened password hashing path |

### Cryptographic Primitives
- **Key derivation:** BIP39 (Bitcoin standard)
- **Encryption:** XChaCha20-Poly1305 (AEAD)
- **Signing:** Ed25519 (RFC 8032)
- **Hashing:** SHA3-256 (or K5-512 for quantum-ready)
- **2FA:** Time-based OTP (RFC 6238)

---

## Integration with Other Projects

### K5 (Post-Quantum Hash)
Use K5-H for password verification:
```python
password_hash = k5_h(password, mem_kib=1024, time_cost=3)
# Balloon-hardened hash resistant to GPU brute-force
```

### Libern (P2P Sync)
Sync vault across user's devices via encrypted P2P:
```
User's Phone 1 ←→ [Libern P2P] ←→ User's Laptop
      (master)                    (replica)
    Ed25519 key signs all syncs
```

### AIOSS (Audit Trail)
Every vault operation logged:
```python
aioss.append(
  type="identity_operation",
  actor=user_id,
  content={
    "operation": "add_passkey",
    "service": "github.com",
    "timestamp": "2026-09-28T12:34:56Z"
  }
)
```

---

## Limitations

- **Requires biometric support** — Touch/Face ID needed for unlock (can fall back to PIN)
- **No server-side account** — If you forget seed + lose device, data is unrecoverable
- **Platform-specific** — Mobile-first; desktop support secondary
- **TOTP import** — Can only import standard TOTP (some services use non-standard formats)

---

## License

MIT — Lois-Kleinner Alpasan

---

## References

1. FIDO2/WebAuthn: https://www.w3.org/TR/webauthn-2/
2. BIP39 Seed Phrases: https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki
3. Shamir's Secret Sharing: https://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing
4. Lois-Kleinner GitHub: https://github.com/kleinnner/Anticloud
