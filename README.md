# ✦ Stellar-Cipher

**Cosmic Cryptography System** — encrypts text with real AES-128, then maps every ciphertext byte onto a real star from an actual astronomical catalog. The mapped stars are connected with a Minimum Spanning Tree to form a unique "constellation" for your message, rendered on an interactive celestial map alongside genuine background stars.

![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)
![Flask](https://img.shields.io/badge/Flask-3.0-black.svg)
![License](https://img.shields.io/badge/License-MIT-green.svg)

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/ab470d0b-9797-4ac0-9749-0ec5372323d7" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/af7f562b-8776-4316-ac55-7c7a292dd12b" />


---

## What it does

Because the star chosen for each byte only depends on the AES key, the byte's position, and its value, decoding independently regenerates the **exact same constellation** from just the encoded string + key — nothing extra needs to be stored server-side.

- Real **AES-128-CBC** encryption 
- Real astronomical data — a magnitude-filtered subset of the **HYG star catalog**
- Deterministic ciphertext → star mapping via SHA-256
- **Minimum Spanning Tree** graph connecting the mapped stars (via `scipy.sparse.csgraph`)
- Interactive canvas map: zoom, pan, hover tooltips with star name / RA / Dec / magnitude
- One-click PNG export of any constellation
- Fully responsive UI, no frontend framework or build step

## Project structure

```
stellar-cipher/
├── backend/
│   ├── app.py                     # Flask entry point (serves API + frontend)
│   ├── config.py                  # all tunable constants
│   ├── requirements.txt
│   ├── encryption/
│   │   ├── encrypt.py             # AES-128-CBC encryption
│   │   └── decrypt.py             # AES-128-CBC decryption
│   ├── stars/
│   │   ├── coordinate_mapper.py   # ciphertext bytes -> real stars
│   │   ├── graph_builder.py       # MST over angular distance
│   │   └── star_generator.py      # loads/caches the HYG catalog
│   ├── data/
│   │   ├── hyg_subset.csv         # real star catalog (included, ready to use)
│   │   └── README.md              # dataset details + how to swap it
│   ├── visualization/
│   │   └── plotter.py             # server-side PNG export (matplotlib)
│   ├── api/
│   │   └── routes.py              # /api/encrypt /api/decrypt /api/noise /api/plot
│   └── utils/
│       └── helpers.py             # key derivation, entropy, JSON envelopes
└── frontend/
    ├── index.html                 # three-panel UI
    ├── css/style.css              # dark cosmic theme, fully responsive
    └── js/script.js               # canvas rendering, API calls, zoom/pan
```

## Getting started

You only need Python. The frontend is served by the same Flask app, so there's one command and one port.

```bash
git clone https://github.com/alishba-batool/stellar-cipher.git
cd stellar-cipher/backend
pip install -r requirements.txt
python app.py
```

Then open **http://localhost:5000** in your browser.

The star catalog (`data/hyg_subset.csv`) is already included, so it works immediately with no extra downloads. 

## How to use it

1. Leave mode on **ENCODE**.
2. Type a message (up to 280 characters).
3. Enter any passphrase as the **Security Key** (min. 4 characters — internally it's SHA-256'd down to a 128-bit AES key).
4. Adjust the **Noise Filter** slider to control how many background stars are drawn.
5. Click **ENCRYPT MESSAGE**. The constellation streams in: real AES-128 ciphertext, mapped onto real sky coordinates, connected by a minimum spanning tree.
6. Hover any bright star to see its name, RA/Dec, magnitude, and the ciphertext byte it represents. Scroll to zoom, drag to pan.
7. Copy the **encoded transmission** string shown after encrypting.
8. Switch to **DECODE**, paste it back in with the same key, and it reconstructs both the original message and the identical constellation.
9. Use the ⭳ export button to download the current view as a PNG.

## Deployment

The app is a single Flask service (API + frontend together) — no separate frontend build/host needed.

**Render.com (free tier, easiest):**
1. Push this repo to GitHub.
2. [render.com](https://render.com) → **New +** → **Web Service** → connect the repo.
3. Set **Root Directory:** `backend`, **Build Command:** `pip install -r requirements.txt`, **Start Command:** `gunicorn -w 2 app:app`.
4. Deploy — Render assigns a public URL and injects `PORT`, which `config.py` already reads.

Also works on Railway.app, PythonAnywhere, or Fly.io. Always run with **gunicorn** in production, not `python app.py` (Flask's dev server isn't meant for public traffic).

## Notes on the crypto

Uses real AES-128-CBC (via `pycryptodome`) — genuinely how you'd encrypt data, minus a production-grade KDF (it uses a single SHA-256 pass instead of PBKDF2/Argon2, which is fine for a learning/portfolio project but shouldn't be treated as production security).

## Tech stack

- **Backend:** Flask, pycryptodome (AES), NumPy + SciPy (MST via `scipy.sparse.csgraph.minimum_spanning_tree`), Matplotlib (PNG export)
- **Frontend:** vanilla HTML/CSS/JS, HTML5 Canvas — no build step, no frameworks

## Author

**Alishba Batool** — BS Data Science student. Built during summer break, inspired by an assembly-language cipher project from coursework, designed and directed with AI-assisted development (Claude).

## License

MIT — see `LICENSE`.
