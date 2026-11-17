# Pramaan Assessment Methodology

Pramaan replaces noisy "scanner guessing" with a deterministic, evidence-driven verification pipeline. The methodology adheres strictly to established technical testing standards: **NIST SP 800-115**, **OWASP ASVS v4.0.3**, **OWASP WSTG v4.2**, **MITRE CWE**, and **FIRST CVSS v3.1**.

---

## 1. The Assessment Pipeline

Every assessment follows a non-negotiable 10-stage execution arc:

$$\text{Authorize} \longrightarrow \text{Scope} \longrightarrow \text{Map} \longrightarrow \text{Detect} \longrightarrow \text{Validate} \longrightarrow \text{Prove} \longrightarrow \text{Score} \longrightarrow \text{Fix} \longrightarrow \text{Re-test} \longrightarrow \text{Report}$$

1. **Authorize**: Cryptographically verify the signed Authorization Record and validity window.
2. **Scope**: Initialize the Scope Guard with target boundaries, loopback CIDRs, and hard-deny filters.
3. **Map**: Run Target Profiler (§7.2) to discover deployed surfaces, authentication models, and available controls.
4. **Detect**: Dispatch domain modules (SAST, DAST, SCA) under rate caps to identify candidate weaknesses.
5. **Validate**: Execute the validation state machine; filter out inapplicable checks or weak single signals.
6. **Prove**: Run the Safe PoC Engine against the local isolated sandbox; confirm deterministic oracles.
7. **Score**: Compute auditable CVSS 3.1 base vectors, RiskRank scores, and the 0–100 Security Posture Score.
8. **Fix**: Generate root cause analysis, remediation guidance, and automated regression test scripts.
9. **Re-test**: Re-run the safe PoC and regression test against the patched commit; close only upon verification.
10. **Report**: Export tamper-evident Executive Summary, Technical Report, Markdown tickets, and SARIF 2.1.0 bundles.

---

## 2. Result Tiers

Every individual check executed by Pramaan terminates in exactly one of five well-defined tiers. Only **Confirmed** findings (T1 and T1.5) enter headline vulnerability counts.

| Tier | Meaning | Verification Mechanism | Example | Headline Count? |
|---|---|---|---|:---:|
| **T1: Confirmed + PoC** | Weakness proved with an active, deterministic oracle | Standalone replayable proof-of-concept script passes against the lab | SSRF proxy fetches the local canary listener; canary receives and records the hit | **Yes** |
| **T1.5: Confirmed by Evidence** | Verifiable weakness that does not require an active exploit script | Observable configuration baseline mismatch or verified dependency reachability | Missing `frame-ancestors 'none'` in response headers; vulnerable package imported and reachable | **Yes** |
| **T2: Probable / Review Packet** | Suspicious signal present, but oracle cannot be safely or deterministically verified | Packaged into an analyst review packet with manual verification checklist | A complex heuristic sink in edge routing where dynamic confirmation requires manual context | **No** (Appendix only) |
| **T3: Roadmap** | Planned checks requiring engines not yet active in the current deployment | Documented as planned coverage | Mobile binary analysis, continuous configuration drift monitoring | **Never** |
| **Verified-Clean** | Check executed against the target and confirmed to be properly defended | Observable security control matched expected security policy | Reverse proxy TLS configuration verified: only TLS 1.2/1.3, strong ciphers, HSTS enabled | Informational |
| **Not-Applicable** | Target profile proves the prerequisite feature does not exist | Profiler evidence attached to the check result | "Role Escalation" check against a deployment that runs in anonymous signed session mode with no roles | Informational |

---

## 3. The Seven Assessment Domains

Pramaan organizes security assessment into seven dedicated domain modules matching the SIH26163 scope domains. Each domain maps directly to industry-standard classifications:

| Domain | Module Key | Check Families | Testing Engines | Standards Mapping |
|---|---|---|---|---|
| **D1: Authentication & Session Management** | `auth_session` | API key entropy, URL key exposure, anonymous/signed session tokens, token replay, cookie flags (`Secure`, `HttpOnly`, `SameSite`), OAuth host header handling, sidecar token expiry | DAST, SAST | ASVS V2/V3, OWASP A07:2021, CWE-287, CWE-384, CWE-613 |
| **D2: Authorization & Access Control** | `authz` | Object-level authorization (BOLA/IDOR) via differential dual-identity testing, tier/role route gating, IPC command origin validation, MCP tool invocation authorization | DAST, SAST | ASVS V4, OWASP API1/API5:2023, OWASP A01:2021, CWE-284, CWE-639, CWE-862 |
| **D3: Input Validation & Data Handling** | `input_validation` | SSRF in proxy handlers (tested only via local canary), reflected/stored/DOM XSS from ingested feed content, path traversal, prototype pollution, unsafe HTML sinks, open redirects, unsanitized inputs reaching LLM prompts | DAST, SAST | ASVS V5, OWASP A03/A10:2021, CWE-79, CWE-918, CWE-22, CWE-1321 |
| **D4: API Security** | `api_security` | OpenAPI/schema contract drift, CORS origin validation, HTTP method tampering, cache-key collision on cached GET endpoints, excessive data exposure, unauthenticated route inventory, WebSocket origin checks | DAST, SAST, SCA | OWASP API Top 10 (2023), ASVS V13, CWE-942, CWE-213, CWE-770 |
| **D5: Client-Side Security Controls** | `client_side` | Content-Security-Policy (CSP) strength (`script-src`, `frame-ancestors`, `object-src`), Subresource Integrity (SRI), service worker scope, `postMessage` origin validation, secrets in client bundles, source-map exposure, Tauri desktop IPC capability scopes | DAST, SAST | ASVS V14, OWASP A05:2021, CWE-1021, CWE-79, CWE-200 |
| **D6: Secure Communication** | `transport` | TLS cipher suites and protocol versions (TLS 1.2/1.3 enforcement), HSTS headers, mixed-content detection, `wss://` enforcement, sidecar loopback-only binding verification, desktop update signature verification | DAST, Config | ASVS V9, OWASP A02:2021, CWE-319, CWE-295 |
| **D7: Data Storage & Privacy Protections** | `data_privacy` | Hardcoded secrets in git history (gitleaks), desktop OS keychain integration, local IndexedDB/browser cache leakage, PII or session tokens in application logs, Redis cache key exposure, telemetry disclosures, transitive dependency reachability (OSV), DPDPA 2023 compliance | SAST, SCA, Config | ASVS V6/V8, OWASP A02/A06:2021, CWE-312, CWE-532, CWE-1104 |

---

## 4. The 8 Mandatory Deliverables (FindingSchema)

In accordance with problem statement requirements, every finding promoted to `CONFIRMED` must populate all eight mandatory fields. If any field is missing, the state machine aborts transition.

| # | Deliverable Field | Source | Validation Requirement |
|---|---|---|---|
| **1** | **Vulnerability Title** | Check catalog template + affected component | Clear, descriptive, standardized headline |
| **2** | **Technical Description** | Observed evidence + check definition | Factual explanation of the root weakness |
| **3** | **Affected Component** | SAST path/line, route endpoint, or `package@version` | Exact code location or endpoint URI |
| **4** | **Severity Rating (CVSS)** | Deterministic CVSS v3.1 calculator | Full vector string + score + severity bucket |
| **5** | **Steps to Reproduce** | Generated from PoC execution steps | Numbered, reproducible reproduction instructions |
| **6** | **Proof-of-Concept (Safe)** | Safe PoC Runner output | Standalone replay script + oracle output + redacted evidence |
| **7** | **Business Impact** | Template mapped to CIA security triad | Impact on data confidentiality, integrity, or system availability |
| **8** | **Remediation Recommendations** | Remediation Engine template + observed facts | Specific code diff, config adjustment, and regression test |

---

## 5. Scoring Formulas

### 5.1 CVSS 3.1 Base Score (Deterministic, Zero AI)
Scores are computed strictly using the standard FIRST CVSS v3.1 specification via the deterministic `cvss` Python library. Base vectors are defined per check in the catalog and refined by observed network reachability and privilege requirements:
- **Critical:** 9.0 – 10.0
- **High:** 7.0 – 8.9
- **Medium:** 4.0 – 6.9
- **Low:** 0.1 – 3.9

### 5.2 Environmental RiskRank Formula
Risk ranking prioritizes confirmed findings for remediation based on environmental exposure and fix complexity:

$$\text{RiskRank} = \frac{\text{CVSS}_{\text{base}} \times \text{Confidence} \times \text{Exposure Factor}}{\text{Fix Effort}}$$

Where:
- **Confidence:** $1.0$ for T1 (PoC verified), $0.9$ for T1.5 (evidenced).
- **Exposure Factor:** $1.0$ for internet-facing defaults, $0.7$ for authenticated-only routes, $0.4$ for local-only components.
- **Fix Effort:** $1.0$ for configuration changes, $2.0$ for localized code patches, $3.5$ for architectural redesign.

### 5.3 Security Posture Score (0–100)
A completely auditable, explainable metric reflecting the defensive posture of the target system:

$$\text{Posture Score} = \text{Coverage} + \text{Exposure} + \text{Remediation} + \text{Evidence Quality}$$

$$\text{Coverage} = \left(\frac{\text{Domains Executed}}{7}\right) \times 25$$

$$\text{Exposure} = \left(1 - \frac{\sum \text{Severity Weights of Open Confirmed Findings}}{\text{Cap } (60)}\right) \times 35$$
*(Severity weights: Critical = 10, High = 6, Medium = 3, Low = 1. Closed findings contribute 0).*

$$\text{Remediation} = \left(\frac{\text{Findings Closed After Re-test}}{\text{Total Confirmed Findings}}\right) \times 25$$

$$\text{Evidence Quality} = \left(\frac{\text{Confirmed Findings with 8 Fields + Passing Replay}}{\text{Total Confirmed Findings}}\right) \times 15$$

> **Edge-Case Honesty Rule:** A scan with zero confirmed vulnerabilities never defaults to 100. Coverage and Evidence Quality formulas remain strictly enforced, and reports explicitly state: *"Verified-clean on N checks; not a guarantee of the absence of all vulnerabilities."*
