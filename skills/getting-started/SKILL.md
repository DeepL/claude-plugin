---
name: getting-started
description: |
  A short first-run tour of DeepL that ends with a real result on the user's own content: a translated
  text or file and, on plans that support it, a first set of saved company terms. Use when the user is new
  to DeepL or just connected it and asks "get started with DeepL", "what can DeepL do?", "show me how
  DeepL works", "help me try DeepL", "set up DeepL" or "onboard me". Not for a concrete translation,
  rewrite or glossary request: handle those directly.
license: MIT
metadata:
  author: DeepL
  version: "1.0"
---

# Getting started with DeepL

## What this skill does

Gets a new user to one real result in a few turns, on their own content, instead of a feature tour.
The user shares something they actually work on, DeepL translates it, and, when the plan allows, DeepL
saves the terms from it that should always be translated the same way. The user leaves knowing what
DeepL did for them and how to ask for it next time.

## Rules for every step

- **Keep the workflow out of sight.** Never tell the user about steps, paths (Pro, Free), plans you
  inferred, tool names or this skill. Write no text before or between tool calls.
- **Show, then ask.** Ask one question per message. Apart from results, write at most three short
  sentences. Every step can be skipped. If the user wants to stop, stop.
- **Keep one conversation language.** Talk in the language of the user's first message to you, the one
  that started this tour, and keep it for the whole tour. Never switch to the target language of a
  translation, and short replies such as a number or a picked option do not change it. Use plain words.
  Do not say "glossary" or "style rule" before step 3, and explain the word the first time you use it.
- **Every translation comes from a DeepL tool.** Never translate, summarize a translation, or rebuild a
  file yourself. If a DeepL tool fails, say what failed and offer the next step.
- **Buttons only for a message that is just a question.** When a message shows anything else, such as
  the introduction, a translation, a table of terms or a file link, write it and ask the question in
  text with two to four numbered options in the same message. The host's tool that asks with clickable
  options hides everything else you write in that message, so use it only when the question is the
  whole message, such as asking for the language of the example.
- **Nothing is saved without a yes.** Show the exact terms first, then create.
- **Terms come only from the user's content.** Each source term must appear in the text you translated.
  Never add terms from general knowledge.
- **No sign-off.** Never end a message with a line such as "Translated with DeepL". The user already
  knows who translated it.
- **Say only what the tool results say** about files, links, limits and saved terms. Do not add details
  such as how long a link works.

## Workflow

### 1. Get something real to translate

Reply right away, without calling any DeepL tool first: the first thing the user sees is this message.
Write it in plain text, without the option tool. Welcome the user, say in two sentences what DeepL is and
what happens next, then ask the question with numbered options. For example:

> Welcome to DeepL! DeepL is a translation AI that translates texts and whole files into many languages
> and keeps the layout of your files. Let's try it on something of yours, it takes about a minute.
>
> What would you like to translate, and into which language?
> 1. Paste a text
> 2. Attach a file
> 3. Show me an example

What the options mean:

1. **Paste a text**, such as an email, a product description or a paragraph from a document.
2. **Attach a file** (Word, PowerPoint, Excel, PDF, HTML, text). DeepL keeps the layout.
3. **Show me an example.**

If the user picks an option without naming a language, ask for the language in the same message that
asks for their text or file, or, for the example, with the option tool and a few common languages plus
"Other". Never guess the language. Infer the user's job and topic from what they share.

- **Example:** use this text: "Northwind Pay lets small shops accept card payments in minutes. Connect
  your Northwind Pay terminal, open the Merchant Hub and set up your first payout. Payouts reach your
  account within two business days."

### 2. Translate it

Before the first DeepL call, check silently what the account can do. Load the DeepL tools if they are
deferred, then pick the path from the tools you see. No tool reports the plan, and the user is not asked.

| What you see | Path |
| --- | --- |
| `list-glossaries` and `create-glossary` are listed | **Pro path.** Call `list-glossaries`. |
| `list-glossaries` is listed, `create-glossary` is not | **Pro path without saving.** Skip step 3a. |
| No glossary tools | **Free path.** |

- **Every language the user asked for, in one call.** Both `translate-text` and `upload-document` take
  several target languages at once, so never translate one language and offer the others later.
- **Text** (pasted or the example): call `translate-text` with `targetLangs` and no `sourceLang`, so
  that DeepL detects it. The result names the detected source language; use it as `sourceLang` in
  step 3. Keep a text under 4,000 characters, because the Free plan limits a request to 5,000. For
  longer text, translate the first part and say so.
- **Free path:** send only the text or file and the target languages. The Free plan rejects
  `formality`, `context`, `customInstructions`, `glossaryId` and `styleId`.
- **File:** call `upload-document` and follow its result. Send the file to the upload URL yourself. If
  you cannot, call `upload-document-show-fallback-widget` when the result says so, and tell the user to
  pick the file in the widget. Then call `get-document-status` until the file is done and
  `download-document` to give the user the link. Do not stop after the upload: the result is the
  downloaded file.

Show the result as it is. Add one sentence on what DeepL did, for example that it kept the layout of
the file.

### 3a. Pro path: save the user's terms

Always offer this step after the first translation on the Pro path. Glossaries shared with the user's
team do not count as the user's own, so they are no reason to skip it.

- **After the example:** the terms are not the user's, so do not save them. In the same message as
  the translation, ask whether DeepL should learn the user's own product names and terms, with the
  numbered options "Paste my text" and "Not now". If
  the user pastes a text, translate it as in step 2, then continue below.
- **After the user's own text or file:**
  1. Pick up to 10 terms from the source text that must be translated the same way every time: product
     and brand names, names of features or plans, and recurring terms of the user's field. For a file,
     use its text if you can read it. Write each term in its dictionary form: in languages with cases,
     such as Russian, German or Polish, use the nominative singular ("Тихая Гавань", not "Тихой Гавани").
     Use the target term from DeepL's translation. A brand or product name keeps its source spelling.
     Put first the terms that DeepL translated, because those are where the user's own wording matters.
  2. Show them as a short table with one translation column per target language. In one sentence, say
     that DeepL can save them as a glossary, a list of terms it applies to every text and file the user
     translates. Then ask, in the same message as the translation and the table, with the numbered
     options "Save these terms", "Change some first" and "Not now".
  3. If the user already has a glossary of their own for this topic, add the terms to it with
     `update-glossary-entries` instead of creating a second one. Otherwise call `create-glossary` with a
     name such as "<Company> terms", then `update-glossary-entries` with the new glossary id, the
     detected source language as `sourceLang`, `targetLang` and the approved entries, once for each
     target language.
  4. Translate the sentence that contains the most saved terms again with `translate-text`, `glossaryId`
     and the same `sourceLang`: a glossary needs an explicit source language. Name the glossary, show
     both versions, one under the other, and mark the terms that changed. If nothing changed, say so
     plainly: DeepL already used these terms, and the glossary keeps them that way in future
     translations.

If a glossary call fails because of permissions or plan limits, say so in one sentence and go to step 4.

### 3b. Free path: show one more thing

Offer one of these as numbered options, whichever fits what the user shared, and do it if they agree:

- A pasted text: translate a real file with its layout kept, as in step 2.
- A file: polish a text the user writes with `correct-text` or `rephrase-text`. The Free plan limits
  such a text to 2,000 characters.

Then name, in one sentence, the terms that DeepL Pro could save for them, such as two terms from their
text. Do not push an upgrade, and do not repeat it.

If a tool reports a limit or that the user needs DeepL Pro, explain it in plain words and suggest a
shorter text.

### 4. Wrap up

In two sentences:

- what DeepL did: the translation, the file, and the glossary name with its number of terms if one
  was saved;
- how to ask next time, as one example request that names DeepL and fits what the user shared, such as
  "Translate this into German with DeepL and my Northwind terms".

Then, in the same message, ask one question about the next step with numbered options the user has not
done yet:

| The user has | Options |
| --- | --- |
| Saved a glossary | Add another language to it · Translate a file with it · That's all |
| Translated a text, no glossary | Translate a file with its layout kept · That's all |
| Translated a file, no glossary | Translate a text · That's all |

If the user picks a step, go back to that step of the workflow. Offer this question once. After
"That's all", stop. Do not list every DeepL feature.

## Notes

- **When the user arrives with a task**, do the task first and skip the tour. Offer step 3 afterwards
  only if it fits.
- **Several target languages:** translate into all of them in step 2, up to 10 in one call, and show the
  before and after in step 3 for each of them.
