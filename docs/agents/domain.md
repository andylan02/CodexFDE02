# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

## Before exploring, read these

- `CONTEXT.md` at the repo root, or
- `CONTEXT-MAP.md` at the repo root if it exists: it points to one `CONTEXT.md` per context. Read each relevant to the topic.
- `docs/adr/`: read ADRs that touch the area you're about to work in.

If these files do not exist, proceed silently. Do not flag their absence or suggest creating them upfront.

## File structure

This repo is currently single-context by default:

```
/
├── CONTEXT.md                  (optional)
├── docs/
│   └── adr/                   (optional)
├── workbench/
├── eval/
├── agent/
├── tests/
└── README.md
```

If a future monorepo or multi-context layout is introduced, a root `CONTEXT-MAP.md` may point to multiple context-specific `CONTEXT.md` files. Until then, use the single-context default.

## Use the glossary's vocabulary

When output names a domain concept, prefer the terms defined in the repo's `CONTEXT.md` or other domain docs. Do not invent synonyms that conflict with the repo's existing terminology.

## Flag ADR conflicts

If an output contradicts an existing ADR, state that explicitly rather than silently overriding it.

> Contradicts ADR-0007 (example), but worth reopening because…
