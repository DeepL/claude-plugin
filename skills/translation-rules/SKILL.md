---
name: translation-rules
description: |
  The standard rules for translating, editing glossaries, rephrasing and proofreading with DeepL —
  apply on every DeepL job. Use when the user asks to translate, localize, rephrase or proofread
  anything, when they say "apply our translation rules" / "translate with our rules", or before any
  glossary, style-rule or formality change.
license: proprietary
metadata:
  author: DeepL
  version: "3.0"
---

# DeepL translation rules (v3)

## What this skill does

Defines how every DeepL job is run: what always goes through DeepL tools, what must be preserved,
when to ask the user, and how glossaries, style rules and formality are chosen. When this skill is
active, follow every rule below on each job.

## Universal rules (every translation)

1. **Never translate URLs.** Localized URLs break links. You may *ask* whether paths should be
   localized (`/en/industries/` → `/de/industries/`), but never localize by default.
2. **Always translate through DeepL tools**, never from your own knowledge: `translate-text` for
   text, `upload-document` → `get-document-status` → `download-document` for files.
3. **Preserve exactly as in the source:** numbers, units, product names, placeholders and tags.
4. **Check glossary coverage before translating.** If a glossary exists for the domain but not for
   this language pair (EN-DE exists, the user wants RU), say so instead of silently translating
   without it.
5. **Don't re-ask for settings that are already set or known.** Apply the formality and style rules
   in force. Only when formality is unknown do you decide — infer it from prior translations, or ask
   (see *Style rules and formality*).
6. **Ask clarifying questions only when errors are expensive** — large batch jobs, regulated
   content. For small translations, just translate.

## Translate text (`translate-text`, `get-source-languages`, `get-target-languages`)

- **Auto-detect the source language** (leave source unset), except when a glossary is applied —
  glossaries require an explicit source language.
- **Ask when the target language is ambiguous** ("translate this"). For English targets use EN-US
  unless the context clearly calls for EN-GB.
- **Never invent support.** Check `get-source-languages` / `get-target-languages` when unsure and
  offer the closest supported option instead of translating from your own knowledge.
- **Pass the content domain as `context`** ("legal contract", "marketing copy"). It improves quality
  and is not itself translated.
- **Ad-hoc steering goes into `customInstructions`** ("keep it punchy") — max 10, each under 300
  characters.

## Translate documents (`upload-document` → `get-document-status` → `download-document`)

- **Supported formats:** docx, pptx, xlsx, pdf, html, txt, srt, xliff.
- **Layout is preserved by DeepL.** Never reconstruct the document yourself.

## Glossaries (`list-glossaries`, `get-glossary-info`, `get-glossary-dictionary-entries`)

- **Auto-select the glossary from the translation context** when the match is clear from its name;
  otherwise ask the user which one to apply.
- **Always tell the user the name of the glossary you used.**

## Editing customizations (create and edit glossaries)

- **Show a diff before any glossary change** — entries added, changed and removed — and get explicit
  confirmation before writing.
- **Extend before creating.** Default to adding entries to the existing glossary for that domain.
  Create a new glossary only when the domain or audience is genuinely different.
- **Source terms from real content only.** When asked to build or extend a glossary from a document
  or past translations, propose candidate pairs extracted from that content and let the user approve
  them. Never invent terminology.

## Style rules and formality (`list-style-rule-sets`, `get-style-rule-set`, `get-custom-instruction`)

- **Apply what is already set** without asking (rule 5 above).
- **When formality is not set or not inferable, confirm the setting with the user** before applying
  it.

## Rephrase and tone (`rephrase-text`)

- **Styles:** academic, business, casual, simple. **Tones:** confident, diplomatic, enthusiastic,
  friendly. One or the other, never both.
- **Keep the original language.** Rephrasing is not translation.

## Proofread (`correct-text`)

- **Fix typos, grammar and punctuation only.** Keep the author's wording and voice.
