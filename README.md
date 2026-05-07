# MyCloud Endpoints

Public endpoint discovery for FusionCloud (MyCloud Drive) clients.

- `v1.json` — signed endpoint config (ed25519 signature over canonical
  payload). Valid 90 days from `issued_at_utc`. Schema: see plan §15.2.
- `RootCa.crt.pem` — public root CA cert used to verify `v1.json`. Identical
  to `runtime/MyCloud.Runtime/Embedded/RootCa.crt.pem` in the FusionCloud repo.

Clients fetch in this order with 5s timeout each:

1. https://gitee.com/firepython/mycloud-endpoints/raw/main/v1.json
2. https://cdn.jsdelivr.net/gh/mgcpngn/mycloud-endpoints@main/v1.json
3. https://raw.githubusercontent.com/mgcpngn/mycloud-endpoints/main/v1.json

Trust is anchored on the **root CA signature**, not the URL. URL hijacking
does not let an attacker forge a config (they would need the root CA
private key).

## Verifying a payload manually

```bash
openssl x509 -in RootCa.crt.pem -pubkey -noout > rootca-pub.pem
# Strip the signature field and re-canonicalize, then:
openssl pkeyutl -verify -pubin -inkey rootca-pub.pem \
  -rawin -in canonical-payload.bin -sigfile signature.bin
```
