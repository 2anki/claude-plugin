# 2anki for Claude

Turn what you're studying into Anki flashcard decks without leaving Claude. Paste notes, attach a CSV, Markdown, HTML or text file, share a web page, or send a photo of handwritten notes. 2anki builds the cards and gives you a preview and a download link for the `.apkg` deck, which you import into Anki, AnkiMobile or AnkiDroid.

## Install

In Claude, add 2anki from the plugin directory. In Claude Code:

```
/plugin marketplace add 2anki/claude-plugin
/plugin install 2anki@2anki
```

## Use it

Ask in your own words. For example:

- Attach `vocab.csv` and write: `Make Anki flashcards from this file.`
- Paste lecture notes and write: `Turn these into cloze cards in a deck called Biology::Cell signalling.`
- `Turn https://en.wikipedia.org/wiki/Beta_blocker into Anki cards about mechanism and side effects.`
- Attach a photo of handwritten notes and write: `Make flashcards from this page.`
- `Show me the decks I've made with 2anki.`

Claude builds the deck with 2anki and replies with the card count, a few sample cards, and the download link. In Claude Code, it then asks whether to download the deck and open it in Anki; say yes and the deck is imported straight away. Add `and open it in Anki` to your request to skip the question.

## What's in the plugin

- **2anki connector** (`.mcp.json`): the hosted MCP server at `https://2anki.net/mcp`. You sign in with your 2anki account through OAuth the first time you use it.
- **`anki-flashcards` skill**: tells Claude to build decks with the 2anki connector when you ask for flashcards, an Anki deck or an `.apkg` file. It covers which tool fits each kind of input, how to prepare CSV files with more than two columns, which card options to set, and how to open the finished deck in Anki.

## Tools

| Tool | What it does |
|---|---|
| `convert_to_deck` | Makes a deck from pasted text, a shared file or a web page |
| `create_deck` | Makes a deck from front/back cards |
| `photo_to_deck` | Reads a photo of notes into cards |
| `list_my_decks` | Lists decks you made with 2anki |
| `get_deck_preview` | Shows cards from a deck |
| `deck_capabilities` | Lists note types and card options |

Decks support basic, two-way, cloze, typed-answer and multiple-choice notes, tags, nested decks (`Parent::Child`) and Anki's built-in text-to-speech.

## Data

The plugin ships no local code. When you agree to open a deck in Anki, Claude Code downloads the `.apkg` into your Downloads folder with `curl` and opens it with your system's open command (`open`, `xdg-open` or `Start-Process`). It sends the content you ask to convert to `https://2anki.net/mcp`, which stores the resulting decks in your 2anki account. Photo reading is processed by Anthropic's API on 2anki's behalf. Free accounts have a monthly card limit and a monthly photo limit.

- Privacy policy: https://2anki.net/documentation/misc/privacy-policy
- Terms of service: https://2anki.net/documentation/misc/terms-of-service
- Docs: https://2anki.net/documentation/reference/mcp
- Support: https://2anki.net/contact

## License

MIT, © 2026 Lær Smart AS
