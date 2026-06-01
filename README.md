# Synapse · Local AI Chat

A lightweight, single-file chat UI for talking to a **locally running LLM** — no build step, no backend, no install. Just open `index.html` in your browser and point it at [LM Studio](https://lmstudio.ai/) or [Ollama](https://ollama.com/).

Everything (HTML, CSS, and JavaScript) lives in one `index.html` file.

Try it here: https://pietercooreman.github.io/Synapse/

## Features

- **Streaming responses** — tokens render live as the model generates them.
- **Conversation memory** — the full chat context is sent on each request.
- **Provider switching** — LM Studio (port `1234`) or Ollama (port `11434`), both via the OpenAI-compatible `/v1` API.
- **Live model list** — models are fetched from the provider's `/v1/models` endpoint, with a refresh button.
- **Markdown + syntax highlighting** — replies render as Markdown with highlighted code blocks.
- **Copy buttons** — copy a whole reply, or copy any individual code block.
- **Dark / light theme** — toggle in the header; the choice (and the code highlight theme) is remembered.
- **Adjustable temperature** — slider from `0` to `2`.
- **Editable system prompt** — change the assistant's behavior from the UI.
- **Persistent history** — your conversation is saved to `localStorage` and restored on reload.
- **Export** — download the conversation as Markdown (`.md`) or JSON (`.json`).
- **Regenerate & stop** — re-run the last reply, or abort an in-progress generation.
- **Clear chat** — reset the conversation.
- **HTML sanitization** — rendered Markdown is sanitized with DOMPurify.

## Quick start

1. Start your local LLM server:
   - **LM Studio:** start the local server and enable **CORS**.
   - **Ollama:** run with cross-origin requests allowed, e.g. set `OLLAMA_ORIGINS="*"`.
2. Open `index.html` in a modern browser (double-click it, or serve it locally).
3. Pick your **Backend** and **Model**, then start chatting.

> No dependencies to install — all libraries are loaded from a CDN at runtime, so an internet connection is needed the first time for the stylesheets and scripts (the LLM itself stays local).

## Configuration

Defaults live in the `configs` object inside `index.html`:

```js
const configs = {
  'lm-studio': { base: "http://127.0.0.1:1234", models: ["local-model"] },
  'ollama':    { base: "http://127.0.0.1:11434", models: [] }
};
```

Change the `base` URLs there if your servers run on different hosts or ports.

## How it connects

Requests are sent to the OpenAI-compatible `POST /v1/chat/completions` endpoint with `stream: true`, and the model list is pulled from `GET /v1/models`. This is the same API exposed by both LM Studio and Ollama, so the same UI works with either.

## Tech stack

- [Bootstrap 5.3](https://getbootstrap.com/) + [Bootstrap Icons](https://icons.getbootstrap.com/) — layout, components, theming.
- [marked](https://marked.js.org/) — Markdown rendering.
- [highlight.js](https://highlightjs.org/) — code syntax highlighting.
- [DOMPurify](https://github.com/cure53/DOMPurify) — HTML sanitization.
- Vanilla JavaScript — streaming via `fetch` + `ReadableStream`, `AbortController` for cancellation, the Clipboard API for copy, and `localStorage` for persistence.

## Browser support

Works in any modern, evergreen browser (Chrome, Edge, Firefox, Safari). Requires support for `fetch` streaming, the Clipboard API, and `localStorage`.

## Privacy

The chatbot talks only to your **local** LLM server. Conversations are stored in your browser's `localStorage` and never leave your machine. The only external requests are to a CDN for the front-end libraries.

## License

MIT
