---
name: release-check
description: Use this skill when finishing a milestone, preparing deployment, writing release notes, or validating a release candidate.
---

# Release Check Skill

## Workflow

1. Identify milestone scope.
2. Confirm all milestone tasks are DONE or explicitly deferred.
3. Run full relevant checks.
4. Review deployment config.
5. Check secrets are not committed.
6. Check migrations.
7. Check approval gates.
8. Check docs.
9. Update changelog/release notes.
10. Document known limitations.

## Release checklist

- tests pass
- Django check passes
- migrations are clean
- env vars documented
- SQLite volume/backup documented
- no secrets committed
- approval gates verified
- external APIs mocked in tests
- deployment instructions updated
