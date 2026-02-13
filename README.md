Strudel Trainer Pro

**A lightweight, single-file IDE wrapper for learning [Strudel](https://strudel.cc/).**

Strudel Trainer Pro creates a bridge between visual music production and code-based algorave workflows. It wraps the official Strudel REPL in a custom interface designed to speed up workflow and assist with syntax learning.

Features

### 1. The Sound Bank
Stop memorizing sample names. The left sidebar features a curated library of:
- **Kicks & Drums:** Basic patterns and euclidean rhythms.
- **Bass & Melody:** Sawtooths, squares, and arpeggios.
- **Effects:** Reverb, delay, and filter syntax.

### 2. Visuals GUI Builder
Coding visuals in Strudel is powerful but parameter-heavy. The **Param Builder** allows you to:
- Select a visual type (Scope, Piano Roll, Spiral, etc.).
- Adjust colors, thickness, scale, and cycles using **Sliders and Dropdowns**.
- Inject the perfectly formatted code into your pattern automatically.

### 3.  Genre Templates
Stuck on a blank screen? The "AI Suggestions" panel (Right Sidebar) creates instant song starters for:
- Techno (Berlin, Minimal, Peak Time)
- Drum & Bass (Liquid, Neurofunk)
- House, Ambient, Trance, and more.

### 4.  Smart Syntax Fixer
The editor includes a basic linter that attempts to:
- Auto-close missing parentheses.
- Remove nested `stack()` calls.
- Fix trailing commas.
- Validate the code structure before sending it to the audio engine.

##  Usage

1. Download the `index.html` file from this repository.
2. Open it in any modern web browser (Chrome/Edge/Firefox).
3. Click items in the library to build a pattern.
4. Press **PLAY** to render the audio via the embedded Strudel engine.

##  Technical Details

This tool is built with **Vanilla JavaScript, HTML, and CSS**. 
- **No Build Step:** No Node.js, Webpack, or dependencies required.
- **Integration:** It communicates with the official Strudel REPL via `iframe` URL hash encoding (Base64).
- **Storage:** No data is sent to external servers; everything runs locally in your browser.

##  Credits

This project is a wrapper/helper tool. All audio generation and language parsing is powered by the incredible **Strudel** project.
- **Strudel:** [https://strudel.cc/](https://strudel.cc/)
- **TidalCycles:** [https://tidalcycles.org/](https://tidalcycles.org/)

##  License

MIT License. Feel free to modify, fork, and use this trainer for workshops or personal use.
