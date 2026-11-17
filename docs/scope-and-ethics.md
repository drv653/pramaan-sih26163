# Scope, Ethics & Rules of Engagement

Pramaan is built for authorized, defensive security evaluations within strictly bounded, isolated test environments. Operating a security assessment platform requires rigorous safeguards to prevent harm to production systems, third parties, and user data.

---

## 1. Rules of Engagement (RoE)

1. **Explicit Prior Authorization:** An assessment cannot initiate without a cryptographically valid, signed Authorization Record detailing the authorizing entity, system owner, assessment scope, and valid time window (`valid_from` to `valid_until`).
2. **Local and Controlled Environments Only:** Testing is restricted strictly to local containers, loopback interfaces (`127.0.0.1`, `::1`), and isolated sandbox networks created via Docker Compose with no external egress.
3. **Bounded & Non-Destructive Probes:** All tests are read-only or canary-based. Payloads verify vulnerability existence without modifying application state beyond planted canary tokens. Destructive operations (database deletion, record purging, denial-of-service) are technically prohibited.
4. **Token-Bucket Rate Limiting:** All outbound probes pass through a token-bucket rate limiter capped at a default of 5 requests per second (configurable up to a maximum safety ceiling).
5. **Immediate Kill Switch:** A global kill switch is accessible across all CLI commands and UI screens. When activated, all active test workers and outbound probes terminate within 2 seconds.

---

## 2. Hard-Deny Target Enforcement

To prevent accidental probes against external services or production installations, the Scope Guard enforces an immutable `HARD_DENY` list directly in the network dispatch pipeline:

```python
HARD_DENY = [
    "worldmonitor.app",
    "*.worldmonitor.app",
    "api.worldmonitor.app",
    # Upstream open intelligence providers & public APIs
    "*.github.com",
    "api.github.com",
    "*.twitter.com",
    "*.x.com",
    "*.reddit.com",
    "*.telegram.org",
    "*.openstreetmap.org"
]
```

Before dispatching any HTTP or network request:
1. The hostname is resolved to its IP address via a stub/local resolver.
2. The destination hostname and resolved IP are validated against `HARD_DENY` and public IP ranges.
3. Any connection attempt to a non-loopback, non-sandbox address triggers an immediate `scope_denied` audit entry and halts execution.
4. HTTP redirects are re-resolved and re-validated on every hop to eliminate DNS rebinding and redirection attacks.

---

## 3. Explicit Non-Goals (PRD §3.2)

Pramaan explicitly rejects features that introduce uncontrolled risk or false claims:

- **NG1 — No testing of any production or third-party system:** `worldmonitor.app`, its subdomains, and upstream data sources are permanently hard-denied.
- **NG2 — No AI-determined validity or severity:** Large language models never decide whether a vulnerability exists or what its CVSS score should be.
- **NG3 — No denial-of-service, load, or stress testing:** System availability risks are evaluated by static architectural analysis (unbounded loops, missing rate limits), never by flooding endpoints.
- **NG4 — No post-exploitation:** No lateral movement, no persistent backdoors, and no data exfiltration beyond planted lab canary tokens.
- **NG5 — No brute-forcing or credential stuffing against real accounts:** Authentication checks use synthetic test identities created specifically for the isolated sandbox.
- **NG6 — No Golden Lab conflation:** Findings produced by the internal test fixture app (Golden Lab) are used solely to measure detection precision and are never attributed to the target application.
- **NG7 — No claims without confirmed evidence:** Unvalidated candidates never appear in headline metrics or executive summaries.
- **NG8 — Zero paid infrastructure:** The entire assessment platform operates using free, open-source, or local air-gapped tooling.

---

## 4. Legal & Regulatory Framework Alignment

The architecture directly addresses applicable Indian statutory frameworks as documented in PRD §18.1:

### 4.1 Information Technology Act, 2000 (Section 43 & Section 66)
Section 43 and Section 66 of the IT Act penalize unauthorized access, data extraction, and denial of access to computer systems. Pramaan enforces:
- Mandatory pre-test Authorization Records before any socket is opened.
- Network-level isolation ensuring tests cannot traverse outside local sandbox boundaries.
- Replayable verification scripts that operate exclusively against pinned, self-hosted instances.

### 4.2 Digital Personal Data Protection Act, 2023 (DPDPA)
In accordance with DPDPA principles of purpose limitation and data minimization:
- Secrets, session tokens, and personal identifiers detected during scanning are redacted prior to persistent storage.
- Evidence vaults use AES-GCM encryption with locally held keys.
- Assessment data and associated evidence are subject to explicit retention controls and complete deletion on demand.

---

## 5. Coordinated Vulnerability Disclosure (CVD)

Pramaan follows responsible disclosure best practices:
- Any novel weakness identified in an upstream open-source project during testing must be reported privately to the upstream maintainers via their official security reporting channel (e.g., repository `SECURITY.md`) prior to any public release.
- Published documentation, live demo prototypes, and demonstration materials contain redacted proofs-of-concept only; no live exploits for unpatched zero-day vulnerabilities are published.
- All scan artifacts are labeled as local lab findings against a specific pinned Git commit SHA.
