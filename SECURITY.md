Supported versions
Active: main branch and latest tagged release.

Security fixes only: previous minor release for 90 days after the next release.

End of life: anything older than 90 days or not listed above.

Tip: Update this section when you tag new releases.

Reporting a vulnerability
Preferred channel: email {your security inbox} or GitHub Security Advisory (Private Report).

Backup channel: open a “Security” issue marked as private if you have GitHub Advanced Security; otherwise, email only.

What to include:

Summary: what you found and where.

Impact: what an attacker could do.

Steps to reproduce: commands, inputs, logs.

Environment: OS, Python version, Youmu‑DOS version/commit.

Proof of concept: minimal, non‑destructive.

Response time:

Acknowledgement: 48 hours.

Initial triage: 5 business days.

Fix or mitigation ETA: shared after triage based on severity.

Safe harbor: we won’t pursue or support legal action for good‑faith research on this project. Don’t violate privacy, cause data loss, or disrupt others.

Disclosure policy
Coordinated disclosure: please do not publicly post details until a fix is released and we’ve agreed on timing.

Embargo window: 30–90 days, depending on severity and complexity.

Credit: we’ll acknowledge reporters in release notes unless you prefer anonymity.

Severity classification
We use CVSS v3.1 as a guide and prioritize practical risk to users.

Critical: remote code execution, auth bypass, supply chain compromise, default credentials.

High: arbitrary file write/read outside sandbox, privilege escalation, denial of service with minimal effort.

Medium: path traversal within user scope, information disclosure requiring specific configuration.

Low: minor hardening issues, verbose errors, weak defaults without exploitation path.

Scope
In scope: Youmu‑DOS core, built‑in commands (filesystem, notepad, date/time), login/session logic, config and persistence, installer/start scripts.

Out of scope: third‑party dependencies’ upstream bugs (we’ll coordinate), user‑written plugins or batch files, forks we don’t maintain.

Fix and release process
Triage: reproduce, assign severity, confirm affected versions.

Mitigation: temporary config guidance if a full fix needs time.

Patch: minimal, auditable change with tests.

Review: security review by a maintainer; avoid introducing new attack surface.

Release: patched version and advisory notes with CVE (if applicable).

Communication: changelog entry, README security notes, and guidance to upgrade.

Hardening guidelines for contributors
Input handling: validate and sanitize all user inputs; avoid eval and unsafe shell calls.

Filesystem safety: restrict writes to project directories; avoid path traversal; use safe path join utilities.

Auth/session: don’t store secrets in plain text; avoid hard‑coded credentials; consider hashing if passwords are added later.

Logging: never log secrets or sensitive content; prefer redaction.

Dependencies: pin versions; review changelogs; run pip audit or similar before release.

Error handling: fail closed; provide clear, non‑sensitive error messages.

Security contact
Primary: {email or alias}

PGP key: {optional fingerprint or URL}

Advisories: GitHub Security Advisories on this repository

Responsible research guidelines
Be careful: don’t exploit beyond what’s necessary to prove impact.

No data loss: use test files or synthetic data.

Coordinate: give us a reasonable window to fix; we’ll keep you updated.
