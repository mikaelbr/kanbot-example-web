---
name: docs-writing
description: How to write and update docs (READMEs, guides, reference, ADRs, code comments) so a reader can finish their task and every claim matches the code. Use when a change adds or edits documentation.
---

# Docs writing

## Process

1. Find the existing docs, their structure and tone, and the code they describe.
2. Name the reader and their task. Lead with what they need to do, then the reference detail.
3. Check every command, flag, path, config key and sample against the repo. Run them where it is safe and cheap. Fix wrong docs you find if in scope; otherwise report them.
4. Match the existing structure, headings and voice. Prefer editing a page over adding one. Update tables of contents, indexes and links you affect.
5. Run the project's docs tooling (link checker, markdown lint, docs build) if it has any. Commit with `docs: …`.

## Writing rules

- Short sentences, active voice, second person, present tense.
- One idea per paragraph. Steps as numbered lists, options as tables.
- Code blocks are copy-pasteable and labelled with a language.
- No marketing language, no "simply" or "just".

## Don't

Change code behaviour. If the docs reveal a bug, report it.

## Report

Which pages to read and what task to try. Claims you could not verify.
