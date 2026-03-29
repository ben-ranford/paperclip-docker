# Contributing

## Patch Documentation Requirement

When any file in `patches/*.patch` is added, removed, renamed, or modified, you must update `docs/patch.md` in the same change.

Minimum expectation:
- Keep one section per patch file.
- Explain what the patch changes and why it exists.
- Remove sections for deleted patches.

PRs that change patch files without updating `docs/patch.md` are considered incomplete.
