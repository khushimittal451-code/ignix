# ⬡ MOLEXPLR — Drug Molecule Explorer Dashboard

A production-grade, AI-powered drug molecule exploration dashboard built with vanilla HTML/CSS/JS and the Anthropic Claude API. Designed for researchers, chemists, and drug discovery teams who need fast, intuitive access to molecular data with real-time AI analysis.

---

## 🖼️ Preview

```
┌─────────────────────────────────────────────────────────────┐
│  ⬡ MOLEXPLR     Explorer | Toxicity | Compare | Library     │
├──────────────┬──────────────────────────┬───────────────────┤
│  Compound    │   Stats Row (4 KPIs)     │  ◈ Claude AI      │
│  Search      ├──────────────────────────┤  Analyst          │
│              │   Molecule Detail Panel  │                   │
│  • Aspirin   │   ─ Visualization        │  AI Chat          │
│  • Caffeine  │   ─ Property Grid        │  with context-    │
│  • Nicotine  │   ─ Lipinski Radar Chart │  aware responses  │
│  • Warfarin  │                          │                   │
│  • ...       │                          │  Quick-ask btns   │
└──────────────┴──────────────────────────┴───────────────────┘
```

---

## ✨ Features

### 🔬 Molecule Explorer
- **Compound library sidebar** with live search by name or SMILES string
- **Color-coded toxicity tags** (Safe / Caution / Toxic) on every compound card
- **Dynamic molecule visualization** rendered as SVG — updates on every selection
- **Physicochemical property grid** displaying logP, MW, H-Donors, H-Acceptors, Rotatable Bonds, and tPSA

### 📊 Data Visualization
- **Lipinski Rule of Five radar chart** drawn natively on HTML Canvas — no chart libraries needed
- **KPI stats row** at the top with library-wide counts (total compounds, non-toxic %, flagged, AI analyses)
- **Color-coded glows** on molecule viz that change based on toxicity classification

### 🤖 AI Integration (Claude API)
- **Claude AI Analyst panel** — context-aware molecular pharmacology assistant
- Automatically loads context for the currently selected compound (name, SMILES, properties, description)
- **Quick-ask buttons** for common queries: Toxicity profile, Drug-likeness, Mechanism, Side effects
- Full conversation history per session
- **Clear chat** button to reset the conversation

### 🎨 Design
- Deep-space biopunk aesthetic with obsidian backgrounds and electric cyan accents
- `DM Mono` + `Outfit` font pairing for scientific/tech feel
- Fully responsive with graceful column collapse on mobile
- CSS-only animations and micro-interactions, no JS animation libraries

---

## 🚀 Quick Start

### 1. Clone or download

```bash
git clone https://github.com/yourname/molexplr.git
cd molexplr
```

### 2. Add your Anthropic API key

Open `index.html` and find the `callClaude()` function. The app calls the Anthropic API directly from the browser. To use this in production, proxy through your own backend.

> **⚠️ Security Note:** Never expose API keys in client-side code in production. Set up a backend proxy (Node.js/Express, FastAPI, etc.) that injects the key server-side.

For local development / demos, the fetch call goes to:
```
https://api.anthropic.com/v1/messages
```

### 3. Open in browser

```bash
# No build step needed — just open the file
open index.html

# Or serve with any static server
npx serve .
python3 -m http.server 8080
```

---

## 📁 Project Structure

```
molexplr/
├── index.html          # Complete single-file app (HTML + CSS + JS)
└── README.md           # This file
```

The entire app is a single self-contained HTML file — no npm, no bundler, no dependencies (fonts are loaded from Google Fonts CDN).

---

## 🧪 Compound Database

The app ships with 7 pre-loaded compounds:

| Compound     | Formula      | MW (g/mol) | Toxicity |
|--------------|--------------|------------|----------|
| Aspirin      | C₉H₈O₄      | 180.16     | ✅ Safe  |
| Caffeine     | C₈H₁₀N₄O₂  | 194.19     | ✅ Safe  |
| Ibuprofen    | C₁₃H₁₈O₂   | 206.28     | ✅ Safe  |
| Paracetamol  | C₈H₉NO₂     | 151.16     | ✅ Safe  |
| Naphthalene  | C₁₀H₈       | 128.17     | ⚠️ Caution |
| Warfarin     | C₁₉H₁₆O₄   | 308.33     | ⚠️ Caution |
| Nicotine     | C₁₀H₁₄N₂   | 162.23     | ❌ Toxic |

### Adding your own compounds

Extend the `compounds` array in `index.html`:

```javascript
{
  id: 7,
  name: 'Your Compound',
  smiles: 'C1CCCCC1',           // SMILES string
  formula: 'C₆H₁₂',
  mw: '84.16',
  toxicity: 'safe',             // 'safe' | 'warn' | 'toxic'
  props: {
    logP: '2.90',
    hDonors: '0',
    hAcceptors: '0',
    rotBonds: '0',
    tPSA: '0'
  },
  lipinski: [29, 0, 0, 0, 0],  // [logP%, hDon%, hAcc%, rotB%, tPSA%] scaled to 0-100
  desc: 'Brief description of the compound and its pharmacological significance.'
}
```

---

## 🔌 API Reference

### Claude Integration

The AI analyst uses the `/v1/messages` endpoint with a dynamic system prompt:

```javascript
const systemPrompt = `You are an expert molecular pharmacologist...
The user is currently viewing: ${selected.name} (SMILES: ${selected.smiles})
Properties: logP=${...}, H-Donors=${...}, ...
Respond concisely in 2–4 sentences...`;
```

**Model used:** `claude-sonnet-4-20250514`

**System prompt injects:**
- Compound name and SMILES
- All physicochemical properties
- Toxicity classification
- Brief pharmacological description

### Backend Proxy (Recommended for Production)

```javascript
// Example Express.js proxy
app.post('/api/claude', async (req, res) => {
  const response = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.ANTHROPIC_API_KEY,
      'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify(req.body)
  });
  const data = await response.json();
  res.json(data);
});
```

Then update the fetch URL in `index.html` to point to `/api/claude`.

---

## 🗺️ Roadmap

- [ ] RDKit.js integration for real SMILES → 2D structure rendering
- [ ] Batch toxicity prediction via Tox21 model API
- [ ] Compound comparison view (side-by-side radar charts)
- [ ] Export report as PDF
- [ ] Import compounds from SDF / CSV files
- [ ] PubChem API integration for live compound lookup

---

## 🛠️ Tech Stack

| Layer        | Technology                          |
|--------------|-------------------------------------|
| Frontend     | Vanilla HTML5, CSS3, JavaScript ES6 |
| Charts       | Native HTML Canvas API              |
| Molecule viz | Inline SVG (procedurally generated) |
| Fonts        | Google Fonts (DM Mono + Outfit)     |
| AI           | Anthropic Claude API (claude-sonnet)|
| Build tool   | None — zero dependencies            |

---

## 📄 License

MIT License — free to use, modify, and distribute.

---

## 🙏 Acknowledgements

- Compound data sourced from public databases (PubChem, ChEMBL)
- Toxicity classifications based on Tox21 dataset annotations
- AI analysis powered by [Anthropic Claude](https://anthropic.com)
