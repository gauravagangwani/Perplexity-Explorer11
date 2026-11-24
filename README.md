# 🔍 Perplexity Explorer

> **See how "surprised" a language model is by your sentence — entirely in the browser. No server, no API key, no install.**

![HTML](https://img.shields.io/badge/HTML-single%20file-orange?style=flat-square&logo=html5)
![JavaScript](https://img.shields.io/badge/JavaScript-plain%20ES%20module-yellow?style=flat-square&logo=javascript)
![Model](https://img.shields.io/badge/Model-distilgpt2%20q8-blueviolet?style=flat-square&logo=huggingface)
![License](https://img.shields.io/badge/license-MIT-green?style=flat-square)

---

## 🚀 Live Demo

Just open `index.html` in any modern browser (Chrome, Edge, Firefox).  
Or enable **GitHub Pages** (`Settings → Pages → branch: main`) for a public URL.

---

## ✨ What It Does

Perplexity Explorer is a **single `index.html` file** that runs a real GPT-2 language model in your browser using [Transformers.js](https://huggingface.co/docs/transformers.js). It teaches you what **perplexity** means by showing all the maths step-by-step.

### 4 interactive sections

| # | Section | What you see |
|---|---|---|
| 1 | **Type a sentence** | Textarea + 3 sample buttons. Click one → auto-fills and calculates. Status line shows *Downloading the model… / Model ready*. |
| 2 | **Token probabilities → Perplexity** | Table of `#`, `Token`, `P`, `1/P` for every token. Then 3 steps: multiply all 1/P values, N = token count, Perplexity = (product)^(1/N). |
| 3 | **What does the model predict next?** | Top 8 next-token predictions as animated horizontal bars. The best guess is highlighted. |
| 4 | **Try it by hand** *(Simulated, not from the model)* | Add/remove tokens, type any name and probability. Live-updating table + perplexity. Three presets to explore. |

---

## 🧮 The Maths

**Perplexity** measures how unpredictable a sentence is to the model:

$$\text{Perplexity} = \left(\prod_{i=1}^{N} \frac{1}{P(w_i)}\right)^{1/N} = \exp\!\left(\frac{1}{N}\sum_{i=1}^{N} -\ln P(w_i)\right)$$

The app always uses the **log-space formula** (right side) to avoid numerical overflow on long sentences.

### Quick sanity checks

| Preset | Probabilities | Expected Perplexity |
|---|---|---|
| I love Python | 0.5, 0.8, 0.2 | **2.32** |
| All 1/8 (8 tokens) | 0.125 × 8 | **8.00** |

---

## 🛠️ How It Works (Technical)

```
Browser
  └─ index.html  (single file, no build step)
       ├─ CSS          — white cards, blue/purple buttons, rounded corners
       ├─ HTML         — 4 sections
       └─ <script type="module">
            ├─ Transformers.js v3  (loaded from CDN, no install)
            │    Model: Xenova/distilgpt2  dtype: q8  (8-bit, ~40 MB)
            ├─ Tokenise  →  [50256 (BOS), token1, token2, …]
            ├─ One forward pass  →  logits [1, seqLen, vocabSize]
            ├─ Stable softmax    →  subtract max before exp()
            └─ Log-space perplexity  →  exp(mean(−ln P))
```

**Key implementation details:**

- **Model loads only once** (lazy — on first button click). Cached by the browser after the first download.
- **BOS token `50256`** is prepended so the very first real token also gets a probability from the position before it.
- **Stable softmax** subtracts the maximum logit before `exp()` — prevents `Infinity` for large logit values.
- **If the model fails to load** → clear red error box. Zero fake probabilities are ever shown.
- **XSS-safe** — all user input is HTML-escaped before being injected into the DOM.

---

## 📂 Project Structure

```
perplexity-explorer/
└── index.html   ← everything: HTML + CSS + JS (775 lines, ~8 KB)
```

No `node_modules`. No build step. No backend. No API key.

---

## 🖥️ Browser Requirements

| Browser | Minimum version |
|---|---|
| Chrome / Edge | 90+ |
| Firefox | 90+ |
| Safari | 15.4+ |

> WebAssembly and ES modules must be enabled (they are by default in all modern browsers).

---

## 📖 Beginner-Friendly

Every non-trivial line in the JavaScript has a short comment explaining what it does. Great for learning:

- How tokenisers split text into token IDs
- What logits are and how softmax turns them into probabilities
- Why perplexity is computed in log space
- How to load and run a Hugging Face model in the browser

---

## 📄 License

MIT — do whatever you like with it.
