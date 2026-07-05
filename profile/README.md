<div align="center">
  <img src="https://dirless.com/dirless.png" width="140" alt="Dirless" />

  # Dirless

  **Linux identity management, LDAP free.**

  [Website](https://dirless.com) · [Docs](https://dirless.com/how-it-works.html) · [FAQ](https://dirless.com/faq.html)
</div>

---

Dirless makes users from a cloud IdP (AWS IAM Identity Center, more providers coming)
first-class Linux citizens on every enrolled server, **without LDAP, FreeIPA, SSSD, or
any replication infrastructure on the critical path.**

A host can always answer "who can log in here?" locally, even if our control plane is
down. No SQLite, no directory service, no lock-in: every repo below is open source and
you can export your data and walk away at any time.

## How it works

```
AWS IAM Identity Center
   │  pull users/groups
   ▼
dirless-syncer ──age-encrypt──► dirless-backend (per-customer HTTP server)
                                       │  serves encrypted snapshots
                                       ▼
                                 dirless-agent (on each enrolled Linux host)
                                       │  decrypts, writes local snapshot
                                       ▼
                                 libnss_exec-crystal ──exec──► dirless-nss-exec
                                       (glibc NSS module → local user/group lookups)
```

## Quick start

```sh
curl -fsSL https://dirless.com/rpm/dirless.repo -o /etc/yum.repos.d/dirless.repo
dnf install -y dirless-cli
dirless-cli enroll
```

Full instructions: [dirless.com/how-it-works.html](https://dirless.com/how-it-works.html)

## Repositories

### Core platform (data plane)

| Repo | What it does |
|---|---|
| [dirless-cli](https://github.com/dirless/dirless-cli) | Enroll a host, export/import identity data, start here |
| [dirless-agent](https://github.com/dirless/dirless-agent) | Runs on every enrolled Linux host, serves local NSS lookups |
| [dirless-backend](https://github.com/dirless/dirless-backend) | Per-customer server storing encrypted snapshots |
| [dirless-syncer](https://github.com/dirless/dirless-syncer) | Pulls users/groups from AWS IAM Identity Center |
| [dirless-connect](https://github.com/dirless/dirless-connect) | Short-lived SSH certificates, no `authorized_keys` management |
| [libnss_exec-crystal](https://github.com/dirless/libnss_exec-crystal) | glibc NSS module used by the agent |

### Management plane

| Repo | What it does |
|---|---|
| [dirless-ops](https://github.com/dirless/dirless-ops) | Admin + customer portal, provisioning API |
| [dirless-website](https://github.com/dirless/dirless-website) | dirless.com marketing site + RPM repo |

### Libraries

Reusable Crystal shards built for Dirless, usable standalone:

| Repo | What it does |
|---|---|
| [trash-panda-db](https://github.com/dirless/trash-panda-db) | Embedded database with B-tree storage, WAL, SQL, and Raft replication, pure Crystal, no SQLite |
| [age-crystal](https://github.com/dirless/age-crystal) | [age](https://age-encryption.org/) encryption (X25519 + ChaCha20-Poly1305) |
| [x509-crystal](https://github.com/dirless/x509-crystal) | X.509 CA + client certificate bundles |
| [crystal-ldap](https://github.com/dirless/crystal-ldap) | LDAP v3 client (BER/ASN.1) |
| [ldap-server.cr](https://github.com/dirless/ldap-server.cr) | LDAP server framework (RFC 4511) |
| [dirless-http](https://github.com/dirless/dirless-http) | HTTP client that targets an IP with a separate TLS SNI hostname |
| [agesh](https://github.com/dirless/agesh) | AGE-encrypted remote shell |

## Why open source

We don't believe in identity lock-in. Every core repo is Apache 2.0, and `dirless-cli`
can export your full user/group data (JSON, LDIF, or passwd format) at any time, with
or without a Dirless subscription. See the [FAQ](https://dirless.com/faq.html) for details.

## Contributing

Each repo has its own `CLAUDE.md`/README with build and spec instructions. Standard
Crystal tooling: `shards install`, `crystal spec`, `ameba`, `crystal tool format`.
Issues and PRs welcome.
