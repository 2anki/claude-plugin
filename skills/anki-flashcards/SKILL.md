---
name: anki-flashcards
description: Build Anki flashcard decks (.apkg) with the 2anki connector. Use this skill whenever the user wants flashcards, an Anki deck, an .apkg file, cloze cards, or study or vocabulary cards. The source can be pasted notes, an attached file (CSV, TSV, Markdown, HTML, text), a web page, a photo of notes, or a topic. Use it even when the user only says "create flashcards" or "make this into Anki" and doesn't name 2anki, and before writing any code to generate an .apkg.
---

# Anki flashcards with 2anki

The user installed 2anki to get Anki decks. When they ask for flashcards, the 2anki connector is the tool they chose for the job, so build the deck with it, not with a script that writes the .apkg (genanki or similar). It's tempting to do the conversion yourself in code, because it looks easy. But a 2anki deck does things a one-off script doesn't:

- it is saved to the user's 2anki account, so `list_my_decks` and `get_deck_preview` can find it later
- the user gets a preview and a no-login download link
- it uses proper note types (basic, two-way, cloze, typed answer, multiple choice), tags, nested decks (`Parent::Child`) and Anki's text-to-speech
- 2anki's parsers handle CSV, Markdown, HTML and Notion structure, and long documents

The tools are on the 2anki connector (names may show with a prefix such as `2anki.net`):

| Tool | Use it for |
|---|---|
| `convert_to_deck` | A document or a web page: pass the text in `text` with `filename` (the extension picks the parser), or a link in `url` |
| `create_deck` | Cards you write yourself: from pasted notes, a topic, or the output of `photo_to_deck` |
| `photo_to_deck` | A photo of handwritten notes, a textbook page or a slide. Returns cards, not a file; build the deck with `create_deck` |
| `deck_capabilities` | The full list of note types and options, when you're unsure what's possible |
| `list_my_decks`, `get_deck_preview` | Finding and showing decks the user already made |

## Pick the path

**Attached file (CSV, TSV, Markdown, HTML, text)**: read it and pass its full contents in `text` to `convert_to_deck`, with `filename` set to the original name. Don't ask the user for a public URL; you already have the content.

- **CSV/TSV headers.** 2anki skips the first row only when the first two columns are `front,back` (or `question,answer`). Any other header becomes a stray first card.
- **Files with more than two columns** (for example `expression,reading,meaning,tags`): rewrite them as two columns before converting, so nothing is lost. Put the prompt on the front and combine the rest on the back, e.g. `会う` → `あう — to meet, to see`. Pass a shared tags column through `options.tags` when the values are the same for every row; otherwise mention that per-row tags weren't kept.
- **Long files**: send the whole file. Use `detail: "summary"` so the response stays small, and tell the user the card count.

**Pasted notes or a topic**: write good cards and pass them to `create_deck`. One fact per card, the question on the front, a short answer on the back. For definitions and dense text, cloze cards (`{{c1::…}}`) often work better. Name the deck with `deckName`, and nest with `::`.

**Web page**: pass it as `url` to `convert_to_deck`. If the user wants only part of the page ("focus on side effects"), say so in your summary, and use `create_deck` with cards you write from the page when `convert_to_deck` can't narrow it.

**Photo**: `photo_to_deck` first, show the user the cards, then `create_deck`.

## Options worth setting

Read the request for these and pass them instead of falling back to defaults:

- two-way, "both directions", vocabulary → `basic-reversed`
- "fill in the blank", definitions, dense facts → `cloze`
- "type the answer" → `input`; quiz with choices → `mcq`
- a deck name or subject → `deckName`, nested with `::` (`Japanese::JLPT N5`)
- tags the user names → `tags`
- "read it aloud", "with audio", pronunciation → text-to-speech. Only when the user asks for it; it adds Anki's `{{tts}}` tag and Anki speaks on the user's device.

## After the deck is built

Tell the user what happened, specifically and briefly: the deck name, the card count (and the note count if they differ, e.g. two-way cards), 2–3 sample cards, and the download link. If 2anki reports a monthly limit, pass on the limit and its reset date as stated. Don't add sales language.

## When not to use 2anki

- The user explicitly wants code: a genanki script, a Python program, a pipeline they will run themselves.
- They want to change their existing Anki collection: review history, due cards, editing notes already in Anki. 2anki makes deck files; it doesn't edit or sync a collection. Say so.
- The 2anki tools aren't available in this conversation (the connector isn't connected or sign-in failed). Tell the user and offer to make the .apkg another way.
