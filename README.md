# Synapse · Local AI Chat

A lightweight, single-file chat UI for talking to a **locally running LLM** — no build step, no backend, no install. Just open `index.html` in your browser and point it at [LM Studio](https://lmstudio.ai/) or [Ollama](https://ollama.com/).

Everything (HTML, CSS, and JavaScript) lives in one `index.html` file.

## Features

- **Streaming responses** — tokens render live as the model generates them.
- **Conversation memory** — the full chat context is sent on each request.
- **Provider switching** — LM Studio (port `1234`) or Ollama (port `11434`), both via the OpenAI-compatible `/v1` API.
- **Live model list** — models are fetched from the provider's `/v1/models` endpoint, with a refresh button.
- **Personas / prompt manager** — save, edit and switch between named system prompts (Coding Assistant, Web Designer, Blog Writer, Musician, or your own) from a dropdown. Switching a persona starts a fresh conversation with its prompt.
- **Voice input** — dictate your message with the microphone button, powered by the browser's native Web Speech API (no libraries or API keys).
- **Markdown + syntax highlighting** — replies render as Markdown with highlighted code blocks and properly sized headings.
- **Copy buttons** — copy a whole reply, or copy any individual code block.
- **HTML live preview** — when a reply contains a full HTML document, preview it in a sandboxed, full-screen modal. Edit the source and re-render on the fly, then copy, download, or open it in a new tab. Each HTML block also gets dedicated **Preview / Download / Copy HTML** buttons in the chat.
- **Dark / light theme** — toggle in the header; the choice (and the code highlight theme) is remembered. Previews always render in a neutral light scheme, independent of the app theme.
- **Adjustable temperature** — slider from `0` to `2`.
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
3. Pick your **Persona**, **Backend** and **Model**, then start chatting.

> **Voice input** requires a Chromium-based browser (Chrome/Edge) and microphone permission. The button hides automatically where the Web Speech API isn't available (e.g. Firefox).

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
- Vanilla JavaScript — streaming via `fetch` + `ReadableStream`, `AbortController` for cancellation, the Clipboard API for copy, the [Web Speech API](https://developer.mozilla.org/docs/Web/API/Web_Speech_API) for voice input, a sandboxed `<iframe>` for HTML preview, and `localStorage` for persistence.

## Browser support

Works in any modern, evergreen browser (Chrome, Edge, Firefox, Safari). Requires support for `fetch` streaming, the Clipboard API, and `localStorage`. **Voice input** additionally needs the Web Speech API (`webkitSpeechRecognition`), available in Chromium-based browsers; elsewhere the microphone button is hidden.

## Privacy

The chatbot talks only to your **local** LLM server. Conversations and personas are stored in your browser's `localStorage` and never leave your machine. The only external requests are to a CDN for the front-end libraries.

> **Note on voice input:** the Web Speech API in Chromium browsers streams audio to Google's servers for transcription — it is not on-device. If that matters for your use case, avoid the microphone button and type instead.

## License

MIT
