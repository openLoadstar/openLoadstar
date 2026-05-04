> 🌐 **English** | **[한국어](SECURITY.ko.md)**

# Security Policy

openLoadstar welcomes vulnerability reports. We appreciate your help in patching issues before they affect other users.

---

## 🔢 Supported Versions

openLoadstar is currently in early development (0.x). The **latest `main` branch is always the security-patch target.**

| Component | Supported version |
|:---|:---|
| spec | main (latest) |
| cli | main (latest) |
| ui | main (latest) |
| mcp | main (latest) |
| openLoadstar (overview) | main (latest) |

> After the formal 1.0 release, we'll establish a per-minor-version security support policy and update this table accordingly.

---

## 🚨 Reporting a Vulnerability

> ⚠️ **Do not report security vulnerabilities through public GitHub Issues.** Public exposure before a patch is released risks exploitation.

### Preferred: GitHub Security Advisories

Each repository's **Security** tab lets you submit a private report. GitHub creates a private channel visible only to maintainers and supports CVE issuance later in the process.

- meta: https://github.com/openLoadstar/openLoadstar/security/advisories/new
- spec: https://github.com/openLoadstar/spec/security/advisories/new
- cli: https://github.com/openLoadstar/cli/security/advisories/new
- ui: https://github.com/openLoadstar/ui/security/advisories/new
- mcp: https://github.com/openLoadstar/mcp/security/advisories/new

### Fallback: Maintainer contact

If GitHub Security Advisories is not workable for you, please reach out via the maintainer's GitHub profile.

- 👤 [@aeolusk](https://github.com/aeolusk)

### What to include in your report

- Affected component and version (or commit hash)
- Vulnerability type and impact (e.g. RCE, information disclosure, DoS)
- Reproduction steps (a minimal PoC if possible)
- Confirmed real-world impact (what data or systems are at risk)
- Possible mitigations, if any

---

## ⏱️ Response Timeline

The schedule below is a **best-effort** target and may shift based on severity and complexity.

| Stage | Target |
|:---|:---|
| Acknowledgement of report | within 48 hours |
| Initial triage and assessment | within 7 days |
| Patch or mitigation released | within 90 days |

---

## 📢 Disclosure Policy

openLoadstar follows **coordinated disclosure**.

1. The reporter and maintainers prepare a patch privately within an agreed timeline.
2. After the patch is released, **the CVE and security advisory** are published through GitHub Security Advisories.
3. Reporters may, if they wish, be credited (real name, handle, or anonymous — your choice).
4. Detailed technical information may be published once users have had enough time to upgrade (typically 30 days after the patch release).

---

## 🛡️ Scope

This policy covers the **official code** in the following repositories:

- `openLoadstar/spec`
- `openLoadstar/cli`
- `openLoadstar/ui`
- `openLoadstar/mcp`
- `openLoadstar/openLoadstar` (overview)

**Out of scope**:
- `.loadstar/` metadata content authored in users' own projects
- Forks or derivative code modified by users (report to the maintainers of that fork)
- External dependencies (e.g. Spring Boot, React, Cobra) — report to those projects' security channels

---

## 🙏 Thanks

Responsible reports from security researchers and contributors protect every openLoadstar user. Sincere thanks to everyone who takes the time to report.
