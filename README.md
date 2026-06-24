# Synapse · Local AI Chat

A lightweight, (almost) single-file chat UI for talking to a **locally running LLM** — no build step, no backend, no install. Just open `index.html` in your browser and point it at [LM Studio](https://lmstudio.ai/) or [Ollama](https://ollama.com/).

The whole app — HTML, CSS, and JavaScript — lives in one `index.html` file. The only extra file is `asp-runner.js`, an optional in-browser Classic ASP/VBScript runtime used by the **ASPPY Expert** persona; you only need it if you use that persona.

### Files

| File | Purpose |
| --- | --- |
| `index.html` | The entire app: UI, styles, and all logic. This is all you need to run Synapse. |
| `asp-runner.js` | Optional in-browser ASP/VBScript runtime (ASPPY on Pyodide), used by the **ASPPY Expert** persona's **Run ASP** button. |
| `rag-data.js`, `build-rag.js` | Legacy build artifacts for the ASPPY reference knowledge. No longer loaded — the reference now lives inside each persona in `index.html` — kept for reference only. |

## Features

- **Multiple chats** — create, switch and delete conversations from a sidebar. Each chat is fully isolated with its own persona, backend, model and temperature.
- **LAN support** — set a custom IP address and port for your backend, so you can connect to Ollama or LM Studio running on a different machine in your network.
- **Streaming responses** — tokens render live as the model generates them.
- **Conversation memory** — the full chat context is sent on each request.
- **Provider switching** — LM Studio or Ollama, both via the OpenAI-compatible `/v1` API.
- **Live model list** — models are fetched from the provider's `/v1/models` endpoint, with a refresh button.
- **Personas / prompt manager** — save, edit and switch between named system prompts (Coding Assistant, Web Designer, Blog Writer, Musician, ASPPY Expert, and more, or your own) from a dropdown. Each persona has a recommended default temperature that is applied automatically when you select it.
- **Per-persona reference knowledge (RAG)** — every persona carries an editable knowledge block that is appended to its system prompt on each request. Built-in personas ship with role-specific guidance (formatting rules, language idioms, design fundamentals, etc.); you can edit it for any persona, or leave it empty, right in the Persona Manager.
- **File attachments** — attach files with the paperclip button, **drag-and-drop** onto the input, or **paste** an image from the clipboard. Images are sent for vision-capable models; recognised text/code files are inlined into the message as fenced code blocks. Up to 10 MB per file.
- **In-browser ASP runner** — with the **ASPPY Expert** persona active, Classic ASP / VBScript code blocks get a **Run ASP** button that executes the page entirely in your browser (no IIS, no server) via the bundled ASPPY runtime running on Pyodide (Python → WebAssembly). Includes a simulated request panel (GET/POST query string & form body), self-submitting form support, and open-in-new-tab.
- **Voice input** — dictate your message with the microphone button, powered by the browser's native Web Speech API (no libraries or API keys).
- **Markdown + syntax highlighting** — replies render as Markdown with highlighted code blocks and properly sized headings.
- **Copy buttons** — copy a whole reply, or copy any individual code block.
- **HTML live preview** — when a reply contains a full HTML document, preview it in a sandboxed, full-screen modal. Edit the source and re-render on the fly, then copy, download, or open it in a new tab. Each HTML block also gets dedicated **Preview / Download / Copy HTML** buttons in the chat.
- **API token (optional)** — provide a bearer token for backends that require authentication; it is stored per provider in `localStorage` and sent on requests.
- **Dark / light theme** — toggle in the header; the choice (and the code highlight theme) is remembered. Previews always render in a neutral light scheme, independent of the app theme.
- **Adjustable temperature** — slider from `0` to `2`, with a warning above `1.0`.
- **Generation stats** — each reply shows elapsed time and a token / tokens-per-second estimate.
- **Persistent history** — all conversations and settings are saved to `localStorage` and restored on reload.
- **Export** — download a conversation as Markdown (`.md`) or JSON (`.json`).
- **Regenerate & stop** — re-run the last reply, or abort an in-progress generation.
- **Clear chat** — reset the conversation.
- **HTML sanitization** — rendered Markdown is sanitized with DOMPurify.

## Quick start

1. Start your local LLM server:
   - **LM Studio:** start the local server and enable **CORS**.
   - **Ollama:** run with cross-origin requests allowed, e.g. set `OLLAMA_ORIGINS="*"`.
2. Open `index.html` in a modern browser (double-click it, or serve it locally).
3. Pick your **Persona**, **Backend** and **Model**, then start chatting.

> **Tip:** for LAN use, enter the IP address and port of the machine running your LLM server (e.g. `192.168.1.100` port `1234`).

> **Remote / WAN use:** browsers block requests from `file://` to remote servers, and HTTPS pages cannot connect to HTTP servers (Mixed Content). Serve Synapse over HTTP locally (e.g. `npx serve .` or `python -m http.server`), then connect. Your LLM server must also allow CORS connections from your browser's origin.

### Multiple parallel sessions

Each browser tab has its own `localStorage`, but tabs sharing the **same URL** also share storage — so switching between chats in one tab affects the others. To run fully isolated sessions side by side, each session needs a **different origin**.

One way to achieve this: host Synapse under multiple subdomains, each pointing to the same `index.html`:

- [synapse1.quickersite.com](http://synapse1.quickersite.com/)
- [synapse2.quickersite.com](http://synapse2.quickersite.com/)
- [synapse3.quickersite.com](http://synapse3.quickersite.com/)
- [synapse4.quickersite.com](http://synapse4.quickersite.com/)
- [synapse5.quickersite.com](http://synapse5.quickersite.com/)

Each URL is a separate origin, so every session gets its own chats, personas and settings. Users can open up to 5 independent sessions this way.

> **Tip:** the same trick works with different ports on localhost (e.g. `http://localhost:3001/`, `http://localhost:3002/`, …).

> **Voice input** requires a Chromium-based browser (Chrome/Edge) and microphone permission. The button hides automatically where the Web Speech API isn't available (e.g. Firefox).

> **Run ASP** (ASPPY Expert persona) downloads the Pyodide runtime (~10 MB) from a CDN on first use, so it needs an internet connection the first time. It runs best when Synapse is served over `http(s)://` rather than opened directly from `file://`.

> No dependencies to install — all libraries are loaded from a CDN at runtime, so an internet connection is needed the first time for the stylesheets and scripts (the LLM itself stays local).

## Tech stack

- [Bootstrap 5.3](https://getbootstrap.com/) + [Bootstrap Icons](https://icons.getbootstrap.com/) — layout, components, theming.
- [marked](https://marked.js.org/) — Markdown rendering.
- [highlight.js](https://highlightjs.org/) — code syntax highlighting.
- [DOMPurify](https://github.com/cure53/DOMPurify) — HTML sanitization.
- [Pyodide](https://pyodide.org/) + ASPPY — loaded on demand by `asp-runner.js` to run Classic ASP/VBScript in the browser (only when the ASPPY Expert persona uses **Run ASP**).
- Vanilla JavaScript — streaming via `fetch` + `ReadableStream`, `AbortController` for cancellation, the Clipboard API for copy/paste, the File and FileReader APIs plus drag-and-drop for attachments, the [Web Speech API](https://developer.mozilla.org/docs/Web/API/Web_Speech_API) for voice input, sandboxed `<iframe>`s for HTML and ASP preview, and `localStorage` for persistence.

## Browser support

Works in any modern, evergreen browser (Chrome, Edge, Firefox, Safari). Requires support for `fetch` streaming, the Clipboard API, and `localStorage`. **Voice input** additionally needs the Web Speech API (`webkitSpeechRecognition`), available in Chromium-based browsers; elsewhere the microphone button is hidden.

## Privacy

The chatbot talks only to your **local** LLM server. Conversations, personas (including their reference knowledge) and settings are stored in your browser's `localStorage` and never leave your machine. The only external requests are to a CDN for the front-end libraries — and, if you use the **Run ASP** feature, an additional one-time CDN download of the Pyodide runtime (~10 MB).

> **Note on voice input:** the Web Speech API in Chromium browsers streams audio to Google's servers for transcription — it is not on-device. If that matters for your use case, avoid the microphone button and type instead.

> **Note on file attachments:** attached files are read locally in your browser and included in the message sent to your local LLM. They are not uploaded anywhere else.

## License

MIT
