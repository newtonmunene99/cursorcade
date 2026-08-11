---
name: grill-with-docs
description: A relentless interview to sharpen a plan or design, which also creates OKF repo knowledge as we go.
disable-model-invocation: true
---

Run a `/grilling` session, using the **domain-modeling** skill.

**OKF deliverables** belong in **repo knowledge bundles**, not `conductor/` by default:

- Resolve `<bundle-root>` per Conductor **Knowledge Bundle Resolution** (`knowledge/` or `<pkg>/knowledge/`)
- Decision concepts: `<bundle-root>/decisions/<slug>.md` (`type: Architecture Decision`)
- See Conductor `templates/knowledge/decision-concept.md` and [OKF v0.1](https://github.com/GoogleCloudPlatform/knowledge-catalog/blob/main/okf/SPEC.md)
- Update `<bundle-root>/decisions/index.md` and `<bundle-root>/log.md`

When the user asks for **project docs** or **knowledge**, scaffold or extend the appropriate repo bundle — discover existing `**/knowledge/index.md` first.

Glossary terms: `conductor/context/product.md` or repo `CONTEXT.md` — not a substitute for OKF concept docs.
