# L5 Narrow / L2 General Classification — MF_SO_PASSWORD_MANAGER
**Platform:** Anticloud | **PAX:** 27B | **IP:** USPTO pending 2026, Anticloud FZ LLE

## L5 Narrow
MF_SO_PASSWORD_MANAGER specializes in Anticloud operational credential management: PAX model
weight encryption keys, AIOSS chain signing keys, service API credentials. Not a consumer
password manager. Scoped to the Anticloud security surface.

## L2 General
Every Anticloud project that needs a secret calls MF_SO_PASSWORD_MANAGER via a uniform Python API.
One vault, all 9 tiers.

## PAX Integration
PAX 27B model weights are encrypted at rest; MF_SO_PASSWORD_MANAGER decrypts the key at runtime,
loads PAX, then immediately zeroes the key from memory.

## AIOSS Audit Relevance
Every credential access event (secret name, accessor module, timestamp — never the secret value)
is AIOSS-chained. Full audit trail of which module accessed which credential and when.

## Regulatory
NIST SP 800-63B (authentication), FIPS 140-2 (AES-256-GCM via OpenSSL), GDPR Art. 32
