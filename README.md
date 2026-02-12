<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Strudel Trainer Pro</title>
    <style>
        :root {
            --bg: #000000;
            --panel: #0a0a0a;
            --border: #1a3a3a;
            --text: #e0e0e0;
            --coral-dark: #8B4513;
            --coral: #FF7F50;
            --coral-light: #FFA07A;
            --teal: #008B8B;
            --teal-light: #20B2AA;
            --code-bg: #0a1a1a;
            --code-text: #d4d4d4;
            --font: 'Consolas', 'Monaco', monospace;
            --error: #ff4444;
            --success: #4CAF50;
            --warning: #ffaa00;
        }

        * { box-sizing: border-box; outline: none; }

        body {
            margin: 0;
            background-color: var(--bg);
            color: var(--text);
            font-family: var(--font);
            height: 100vh;
            display: grid;
            grid-template-columns: 300px 1fr 320px;
            grid-template-rows: 100vh;
            overflow: hidden;
        }

        aside.left-col {
            background: var(--panel);
            border-right: 1px solid var(--border);
            display: flex; 
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }
        
        .header { 
            padding: 15px; 
            border-bottom: 1px solid var(--border); 
            color: var(--coral); 
            font-weight: bold; 
            letter-spacing: 1px;
            flex-shrink: 0;
        }
        
        .lib-scroll { 
            flex: 1; 
            overflow-y: auto; 
            padding: 10px;
        }
        
        details { margin-bottom: 5px; border: 1px solid var(--border); }
        summary { 
            padding: 8px; cursor: pointer; background: #080808; 
            font-size: 11px; color: #888; font-weight: bold; list-style: none;
            display: flex; justify-content: space-between;
        }
        summary:hover { color: var(--coral); }
        summary::after { content: '+'; }
        details[open] summary::after { content: '-'; }

        .lib-item {
            background: #0f1f1f; padding: 8px; border-bottom: 1px solid var(--border);
            cursor: pointer;
            transition: all 0.2s;
        }
        .lib-item:hover {
            background: #152525;
            border-left: 3px solid var(--coral);
        }
        .lib-item.selected {
            background: #1a2525;
            border-left: 3px solid var(--coral);
        }
        
        .lib-name { 
            font-size: 11px; 
            color: var(--coral); 
            font-weight: bold;
            margin-bottom: 8px;
        }
        
        .lib-code { 
            font-size: 9px; 
            color: var(--teal-light); 
            font-family: var(--font);
            margin-bottom: 8px;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }
        
        .btn-add {
            background: var(--coral);
            color: black;
            border: none;
            padding: 6px 12px;
            font-size: 10px;
            cursor: pointer;
            font-family: inherit;
            font-weight: bold;
            width: 100%;
            margin-top: 5px;
        }
        .btn-add:hover { background: var(--coral-light); }

        .param-builder {
            background: #0a1515;
            border: 1px solid var(--border);
            padding: 8px;
            margin-top: 8px;
            display: none;
        }
        .param-builder.active { display: block; }
        
        .param-row {
            display: flex;
            gap: 5px;
            margin-bottom: 5px;
            align-items: center;
        }
        
        .param-label {
            font-size: 9px;
            color: #888;
            width: 80px;
            flex-shrink: 0;
        }
        
        .param-input {
            flex: 1;
            background: #0f1f1f;
            color: var(--text);
            border: 1px solid var(--border);
            padding: 4px;
            font-family: inherit;
            font-size: 9px;
        }
        
        .param-select {
            flex: 1;
            background: #0f1f1f;
            color: var(--text);
            border: 1px solid var(--border);
            padding: 4px;
            font-family: inherit;
            font-size: 9px;
        }
        
        .btn-apply {
            background: var(--teal);
            color: white;
            border: none;
            padding: 5px;
            font-size: 9px;
            cursor: pointer;
            font-family: inherit;
            width: 100%;
            margin-top: 5px;
        }
        .btn-apply:hover { background: var(--teal-light); }

        main {
            display: flex; 
            flex-direction: column;
            background: #000;
            height: 100vh;
            overflow: hidden;
        }

        .editor-container {
            height: 350px;
            background: var(--code-bg);
            border-bottom: 1px solid var(--border);
            display: flex; 
            flex-direction: column;
            flex-shrink: 0;
        }
        
        .editor-header {
            background: #0d1d1d; 
            padding: 5px 10px; 
            font-size: 10px; 
            color: var(--teal-light);
            display: flex; 
            justify-content: space-between;
            flex-shrink: 0;
        }
        
        .status-indicator {
            display: flex;
            align-items: center;
            gap: 5px;
            font-size: 10px;
        }
        
        .status-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: #555;
        }
        .status-dot.checking { background: var(--warning); animation: pulse 1s infinite; }
        .status-dot.valid { background: var(--success); }
        .status-dot.error { background: var(--error); }
        
        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.5; }
        }

        #code-input {
            flex: 1;
            background: var(--code-bg); 
            color: var(--code-text);
            border: none; 
            padding: 15px; 
            font-family: var(--font); 
            font-size: 13px;
            resize: none; 
            line-height: 1.6;
            overflow-y: auto;
        }
        #code-input:focus { outline: 1px solid var(--teal); }
        #code-input.has-error { border-left: 3px solid var(--error); }
        #code-input.valid { border-left: 3px solid var(--success); }

        .error-panel {
            background: #1a0000;
            border-bottom: 2px solid var(--error);
            padding: 0;
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.3s;
            display: flex;
            flex-direction: column;
            flex-shrink: 0;
        }
        .error-panel.show { max-height: 150px; }
        
        .error-list {
            flex: 1;
            overflow-y: auto;
            padding: 10px;
        }
        
        .error-item {
            background: #2a0000;
            border-left: 3px solid var(--error);
            padding: 6px;
            margin-bottom: 6px;
            font-size: 10px;
        }
        .error-title {
            color: var(--error);
            font-weight: bold;
            margin-bottom: 3px;
        }
        .error-desc {
            color: #ff8888;
            margin-bottom: 3px;
        }

        .toolbar {
            padding: 10px; 
            background: var(--panel); 
            border-bottom: 1px solid var(--border);
            display: flex; 
            gap: 10px; 
            align-items: center;
            flex-shrink: 0;
        }
        
        .btn-main {
            background: var(--coral-dark); 
            border: none; 
            color: white; 
            font-weight: bold;
            padding: 8px 20px; 
            font-family: inherit; 
            cursor: pointer; 
            font-size: 12px;
            transition: 0.2s;
        }
        .btn-main:hover { background: var(--coral); }
        .btn-main.btn-play { background: var(--teal); }
        .btn-main.btn-play:hover { background: var(--teal-light); }
        .btn-main.btn-stop { background: var(--coral-dark); }
        .btn-main.btn-stop:hover { background: var(--coral); }
        .btn-main.btn-check { background: #2196F3; }

        .strudel-container { 
            flex: 1;
            background: #000;
            min-height: 0;
            position: relative;
        }
        
        iframe {
            width: 100%;
            height: 100%;
            border: none;
        }

        aside.right-col {
            background: var(--panel);
            border-left: 1px solid var(--border);
            display: flex; 
            flex-direction: column;
            height: 100vh;
            overflow: hidden;
        }

        .ai-suggestions {
            flex: 1;
            overflow-y: auto;
            border-bottom: 1px solid var(--border);
        }

        .suggestion-header {
            padding: 10px;
            background: #0f1f1f;
            border-bottom: 1px solid var(--border);
            display: flex;
            justify-content: space-between;
            align-items: center;
            position: sticky;
            top: 0;
            z-index: 10;
        }

        .suggestion-title {
            color: var(--coral);
            font-size: 11px;
            font-weight: bold;
        }

        .style-selector {
            padding: 10px;
            background: #0a1515;
        }

        .style-label {
            color: #888;
            font-size: 10px;
            margin-bottom: 5px;
        }

        .style-dropdown {
            width: 100%;
            background: #0f1f1f;
            color: var(--text);
            border: 1px solid var(--border);
            padding: 6px;
            font-family: inherit;
            font-size: 10px;
        }

        .suggestions-list {
            padding: 10px;
        }

        .suggestion-card {
            background: #0f1f1f;
            border: 1px solid var(--border);
            margin-bottom: 8px;
            padding: 8px;
            cursor: pointer;
            transition: 0.2s;
        }
        .suggestion-card:hover {
            border-color: var(--coral);
            background: #152525;
        }

        .suggestion-name {
            color: var(--coral);
            font-size: 11px;
            font-weight: bold;
            margin-bottom: 4px;
        }

        .suggestion-desc {
            color: #888;
            font-size: 9px;
            margin-bottom: 6px;
        }

        .suggestion-preview {
            background: #000;
            padding: 4px;
            color: var(--teal-light);
            font-size: 8px;
            border-left: 2px solid var(--teal);
            max-height: 40px;
            overflow: hidden;
        }

        ::-webkit-scrollbar { width: 6px; }
        ::-webkit-scrollbar-track { background: #000; }
        ::-webkit-scrollbar-thumb { background: var(--teal); }
        ::-webkit-scrollbar-thumb:hover { background: var(--teal-light); }
    </style>
</head>
<body>

    <aside class="left-col">
        <div class="header">SOUND BANK</div>
        <div class="lib-scroll" id="library-container"></div>
    </aside>

    <main>
        <div class="editor-container">
            <div class="editor-header">
                <span>PATTERN BUILDER // STRUDEL CODE</span>
                <div class="status-indicator">
                    <div class="status-dot" id="status-dot"></div>
                    <span id="status-text" style="color:var(--coral);">READY</span>
                </div>
            </div>
            <textarea id="code-input" spellcheck="false">stack(
  s("bd*4").shape(0.4),
  note("c3 e3 g3 b3").s("sawtooth").lpf(800)
)</textarea>
        </div>

        <div class="error-panel" id="error-panel"></div>

        <div class="toolbar">
            <button class="btn-main btn-check" onclick="validateAndFix()">✓ FIX & CHECK</button>
            <button class="btn-main btn-play" onclick="loadInStrudel()">▶ PLAY</button>
            <button class="btn-main btn-stop" onclick="stopStrudel()">⏹ STOP</button>
            <button class="btn-main" onclick="clearCode()">🗑 NEW</button>
        </div>

        <div class="strudel-container">
            <iframe id="strudel-frame" src="https://strudel.cc/" allow="autoplay; midi"></iframe>
        </div>
    </main>

    <aside class="right-col">
        <div class="ai-suggestions">
            <div class="suggestion-header">
                <span class="suggestion-title">🎵 COMPLETE SONGS</span>
            </div>
            <div class="style-selector">
                <div class="style-label">GENRE:</div>
                <select class="style-dropdown" id="style-select" onchange="generateSuggestions()">
                    <option value="techno">Techno</option>
                    <option value="house">House</option>
                    <option value="ambient">Ambient</option>
                    <option value="dnb">Drum & Bass</option>
                    <option value="acid">Acid</option>
                    <option value="trance">Trance</option>
                    <option value="minimal">Minimal</option>
                    <option value="experimental">Experimental</option>
                </select>
            </div>
            <div class="suggestions-list" id="suggestions-list"></div>
        </div>
    </aside>

<script>
    let strudelFrame = null;
    
    window.addEventListener('load', () => {
        strudelFrame = document.getElementById('strudel-frame');
        generateSuggestions();
    });

    function loadInStrudel() {
        const code = document.getElementById('code-input').value;
        const encoded = btoa(unescape(encodeURIComponent(code)));
        strudelFrame.src = `https://strudel.cc/?mode=base64#${encoded}`;
        
        const statusDot = document.getElementById('status-dot');
        const statusText = document.getElementById('status-text');
        statusDot.className = 'status-dot valid';
        statusText.textContent = 'PLAYING ♪';
        statusText.style.color = 'var(--success)';
    }

    function stopStrudel() {
        const empty = btoa(unescape(encodeURIComponent('// stopped')));
        strudelFrame.src = `https://strudel.cc/?mode=base64#${empty}`;
        const statusDot = document.getElementById('status-dot');
        const statusText = document.getElementById('status-text');
        statusDot.className = 'status-dot';
        statusText.textContent = 'STOPPED';
        statusText.style.color = 'var(--coral)';
    }

    function clearCode() {
        document.getElementById('code-input').value = 'stack(\n  s("bd*4")\n)';
        stopStrudel();
        const statusDot = document.getElementById('status-dot');
        const statusText = document.getElementById('status-text');
        statusDot.className = 'status-dot';
        statusText.textContent = 'READY';
        statusText.style.color = 'var(--coral)';
    }

    // COMPLETE SONG TEMPLATES WITH VISUALS
    const trackTemplates = {
        techno: [
            {
                name: "Berlin Warehouse",
                desc: "Dark techno with scope visual",
                code: `stack(
  s("bd*4").shape(0.6).gain(1.2),
  s("~ ~ rim ~").delay(0.25),
  s("hh*16").gain(0.5).hpf(8000),
  note("c2*8").s("sawtooth").lpf(sine.range(200,1200).fast(4))
).room(0.3).scope()`
            },
            {
                name: "Minimal Techno Loop",
                desc: "Sparse groove with pianoroll",
                code: `stack(
  s("bd ~ ~ ~"),
  s("~ ~ ~ hh"),
  note("c1").s("sine").gain(1.5),
  s("~ cp ~ ~").gain(0.7)
).delay(0.125).pianoroll()`
            },
            {
                name: "Peak Time Energy",
                desc: "Driving with spectrum analyzer",
                code: `stack(
  s("bd*4").gain(1.3).shape(0.5),
  s("~ sd ~ sd").gain(0.9),
  s("hh*8, ~ oh ~ oh"),
  note("c2 ~ e2 ~").s("sawtooth").lpf(1500)
).room(0.5).scope()`
            }
        ],
        house: [
            {
                name: "Deep House Groove",
                desc: "Warm with piano visual",
                code: `stack(
  s("bd ~ ~ ~").shape(0.3),
  s("~ ~ sd ~"),
  s("hh*8").gain(0.6),
  note("c2 eb2 g2 bb2").s("sine").slow(2).gain(1.2)
).room(1.5).pianoroll()`
            },
            {
                name: "Tech House Banger",
                desc: "Filtered bass energy",
                code: `stack(
  s("bd ~ ~ bd ~ ~ ~ ~").shape(0.4),
  s("~ ~ sd ~"),
  s("hh*16").gain(sine.range(0.3,0.7)),
  note("c2*16").s("sawtooth").lpf(sine.range(400,1600).fast(2))
).room(0.4).scope()`
            }
        ],
        ambient: [
            {
                name: "Space Drift",
                desc: "Ethereal pads with scope",
                code: `stack(
  note("c3 e3 g3").s("sawtooth").lpf(400).room(3).slow(4),
  note("c2").s("sine").gain(0.8).slow(2),
  note("g4 e4 c4").s("triangle").lpf(1000).delay(0.5).slow(8)
).room(2.5).scope()`
            },
            {
                name: "Deep Ocean",
                desc: "Evolving textures",
                code: `stack(
  note("c2 f2 g2").s("sine").slow(8).room(3),
  note("c4 d4 e4 g4").s("sawtooth").lpf(sine.range(200,800).slow(8)).slow(4),
  s("~").gain(0.1).delay(0.9).room(3)
).slow(2).scope()`
            }
        ],
        dnb: [
            {
                name: "Liquid Breaks",
                desc: "Smooth DnB with visuals",
                code: `stack(
  s("bd*2 ~ bd*2 ~").fast(2).gain(1.1),
  s("~ sd ~ sd").fast(2),
  s("hh*8").fast(2).gain(0.6),
  note("c4 e4 g4 b4").s("sine").lpf(2000).delay(0.3)
).room(1).pianoroll()`
            },
            {
                name: "Neurofunk Bass",
                desc: "Heavy reese",
                code: `stack(
  s("bd*2 ~ bd*2 ~").fast(2).shape(0.5),
  s("~ sd ~ sd").fast(2).gain(1.2),
  note("c1*8").s("sawtooth").lpf(sine.range(100,400).fast(8)).shape(0.3)
).fast(2).scope()`
            }
        ],
        acid: [
            {
                name: "303 Classic",
                desc: "TB-303 squelch",
                code: `stack(
  s("bd*4").shape(0.4),
  s("~ ~ sd ~"),
  note("c2 c3 eb2 c3").s("sawtooth").lpf(sine.range(200,2500).fast(4)).resonance(12)
).delay(0.25).scope()`
            },
            {
                name: "Acid Trance",
                desc: "Uplifting acid",
                code: `stack(
  s("bd*4").gain(1.2),
  s("~ sd ~ sd"),
  s("hh*16").gain(0.5),
  note("c3 e3 g3 c4 e4").s("sawtooth").lpf(sine.range(400,3000).fast(2))
).room(0.8).pianoroll()`
            }
        ],
        trance: [
            {
                name: "Uplifting Anthem",
                desc: "Epic trance with piano",
                code: `stack(
  s("bd*4").shape(0.3),
  s("~ ~ sd ~"),
  s("hh*16").gain(0.6),
  note("c4 e4 g4 b4 c5 b4 g4 e4").s("sawtooth").lpf(1200).delay(0.5)
).room(1.5).pianoroll()`
            },
            {
                name: "Goa Trance",
                desc: "Psychedelic layers",
                code: `stack(
  s("bd*4").gain(1.1),
  note("c3*16").s("sawtooth").lpf(sine.range(300,2000).fast(8)),
  note("c4 d4 e4 f4 g4").s("triangle").delay(0.375).slow(2)
).room(1).scope()`
            }
        ],
        minimal: [
            {
                name: "Micro House",
                desc: "Sparse clicks",
                code: `stack(
  s("bd ~ ~ ~ ~ ~ bd ~"),
  s("~ ~ ~ ~ ~ hh ~ ~"),
  note("~ c2 ~ ~ ~ e2 ~ ~").s("sine"),
  s("~ ~ ~ ~ cp ~ ~ ~").gain(0.4)
).delay(0.125).scope()`
            }
        ],
        experimental: [
            {
                name: "Generative Chaos",
                desc: "Random evolving",
                code: `stack(
  s("bd*4").sometimes(x=>x.speed(2)),
  note(scale("c:minor")).struct("x*8").s("sawtooth").lpf(rand.range(200,2000)),
  s("hh*16").rarely(x=>x.gain(2))
).room(1).scope()`
            },
            {
                name: "Algorithmic Pulse",
                desc: "Mathematical patterns",
                code: `stack(
  s("bd").euclidean(3,8),
  s("sd").euclidean(5,8),
  s("hh").euclidean(7,16),
  note("c2 eb2 g2").euclidean(4,8).s("sine")
).pianoroll()`
            }
        ]
    };

    function generateSuggestions() {
        const style = document.getElementById('style-select').value;
        const suggestions = trackTemplates[style] || [];
        const container = document.getElementById('suggestions-list');
        
        container.innerHTML = '';
        suggestions.forEach(sug => {
            const div = document.createElement('div');
            div.className = 'suggestion-card';
            div.onclick = () => {
                document.getElementById('code-input').value = sug.code;
                validateAndFix();
                setTimeout(() => loadInStrudel(), 200);
            };
            div.innerHTML = `
                <div class="suggestion-name">${sug.name}</div>
                <div class="suggestion-desc">${sug.desc}</div>
                <div class="suggestion-preview">${sug.code.substring(0, 50)}...</div>
            `;
            container.appendChild(div);
        });
    }

    // VISUAL PARAMETER DEFINITIONS
    const visualParams = {
        pianoroll: {
            cycles: { type: 'number', default: 4, min: 1, max: 16, desc: 'Cycles to display' },
            playhead: { type: 'number', default: 0.5, min: 0, max: 1, step: 0.1, desc: 'Playhead position' },
            vertical: { type: 'boolean', default: false, desc: 'Vertical display' },
            labels: { type: 'boolean', default: false, desc: 'Show labels' },
            flipTime: { type: 'boolean', default: false, desc: 'Reverse time' },
            flipValues: { type: 'boolean', default: false, desc: 'Reverse values' },
            overscan: { type: 'number', default: 1, min: 0, max: 8, desc: 'Look-ahead cycles' },
            hideNegative: { type: 'boolean', default: false, desc: 'Hide negative time' },
            smear: { type: 'boolean', default: false, desc: 'Solid trace' },
            fold: { type: 'boolean', default: false, desc: 'Full width notes' },
            active: { type: 'color', default: '#FFCA28', desc: 'Active color' },
            inactive: { type: 'color', default: '#7491D2', desc: 'Inactive color' },
            background: { type: 'color', default: 'transparent', desc: 'Background' },
            playheadColor: { type: 'color', default: 'white', desc: 'Playhead color' },
            fill: { type: 'boolean', default: false, desc: 'Fill notes' },
            fillActive: { type: 'boolean', default: false, desc: 'Fill active' },
            stroke: { type: 'boolean', default: false, desc: 'Stroke notes' },
            strokeActive: { type: 'boolean', default: false, desc: 'Stroke active' },
            hideInactive: { type: 'boolean', default: false, desc: 'Hide inactive' },
            colorizeInactive: { type: 'boolean', default: true, desc: 'Color inactive' },
            fontFamily: { type: 'text', default: 'monospace', desc: 'Font family' },
            minMidi: { type: 'number', default: 10, min: 0, max: 127, desc: 'Min MIDI note' },
            maxMidi: { type: 'number', default: 90, min: 0, max: 127, desc: 'Max MIDI note' },
            autorange: { type: 'boolean', default: false, desc: 'Auto range' }
        },
        punchcard: {
            cycles: { type: 'number', default: 4, min: 1, max: 16, desc: 'Cycles to display' },
            playhead: { type: 'number', default: 0.5, min: 0, max: 1, step: 0.1, desc: 'Playhead position' },
            vertical: { type: 'boolean', default: false, desc: 'Vertical display' },
            labels: { type: 'boolean', default: false, desc: 'Show labels' },
            flipTime: { type: 'boolean', default: false, desc: 'Reverse time' },
            flipValues: { type: 'boolean', default: false, desc: 'Reverse values' }
        },
        spiral: {
            stretch: { type: 'number', default: 1, min: 0.1, max: 4, step: 0.1, desc: 'Rotations per cycle' },
            size: { type: 'number', default: 300, min: 100, max: 800, desc: 'Diameter' },
            thickness: { type: 'number', default: 2, min: 1, max: 10, desc: 'Line thickness' },
            cap: { type: 'select', default: 'butt', options: ['butt', 'round', 'square'], desc: 'Line cap' },
            inset: { type: 'number', default: 3, min: 0, max: 10, desc: 'Starting rotations' },
            playheadColor: { type: 'color', default: 'white', desc: 'Playhead color' },
            playheadLength: { type: 'number', default: 0.02, min: 0, max: 1, step: 0.01, desc: 'Playhead length' },
            playheadThickness: { type: 'number', default: 2, min: 1, max: 10, desc: 'Playhead thickness' },
            padding: { type: 'number', default: 20, min: 0, max: 100, desc: 'Padding' },
            steady: { type: 'number', default: 0, min: 0, max: 1, step: 0.01, desc: 'Steadiness' },
            activeColor: { type: 'color', default: '#FFCA28', desc: 'Active color' },
            inactiveColor: { type: 'color', default: '#7491D2', desc: 'Inactive color' },
            colorizeInactive: { type: 'boolean', default: false, desc: 'Color inactive' },
            fade: { type: 'boolean', default: true, desc: 'Fade past/future' },
            logSpiral: { type: 'boolean', default: false, desc: 'Logarithmic' }
        },
        scope: {
            align: { type: 'boolean', default: true, desc: 'Align to zero' },
            color: { type: 'color', default: 'white', desc: 'Line color' },
            thickness: { type: 'number', default: 3, min: 1, max: 10, desc: 'Line thickness' },
            scale: { type: 'number', default: 0.25, min: 0.1, max: 2, step: 0.05, desc: 'Y-axis scale' },
            pos: { type: 'number', default: 0.5, min: 0, max: 1, step: 0.1, desc: 'Y position' },
            trigger: { type: 'number', default: 0, min: -1, max: 1, step: 0.1, desc: 'Trigger value' }
        },
        pitchwheel: {
            hapcircles: { type: 'number', default: 12, min: 1, max: 24, desc: 'HAP circles' },
            circle: { type: 'number', default: 1, min: 0, max: 5, desc: 'Circle size' },
            edo: { type: 'number', default: 12, min: 1, max: 24, desc: 'EDO divisions' },
            root: { type: 'text', default: 'C', desc: 'Root note' },
            thickness: { type: 'number', default: 2, min: 1, max: 10, desc: 'Line thickness' },
            hapRadius: { type: 'number', default: 100, min: 50, max: 300, desc: 'HAP radius' },
            mode: { type: 'select', default: 'chromatic', options: ['chromatic', 'major', 'minor'], desc: 'Scale mode' },
            margin: { type: 'number', default: 20, min: 0, max: 100, desc: 'Margin' }
        },
        spectrum: {
            thickness: { type: 'number', default: 3, min: 1, max: 10, desc: 'Line thickness' },
            speed: { type: 'number', default: 1, min: 0.1, max: 5, step: 0.1, desc: 'Scroll speed' },
            min: { type: 'number', default: -80, min: -100, max: 0, desc: 'Min dB' },
            max: { type: 'number', default: 0, min: -50, max: 20, desc: 'Max dB' }
        }
    };

    // VALIDATED PATTERNS WITH PARAM BUILDERS
    const patterns = {
        "KICKS": [
            { name: "Basic 4/4", code: `s("bd*4")` },
            { name: "Shaped", code: `s("bd*4").shape(0.4)` },
            { name: "Hard", code: `s("bd*4").gain(1.2).shape(0.6)` }
        ],
        "DRUMS": [
            { name: "Kick + Snare", code: `s("bd ~ sd ~")` },
            { name: "Hats", code: `s("hh*8").gain(0.6)` },
            { name: "Full Beat", code: `s("bd*4"),\n  s("~ sd ~ sd"),\n  s("hh*8")` }
        ],
        "BASS": [
            { name: "Sub", code: `note("c1").s("sine").gain(1.2)` },
            { name: "Saw", code: `note("c2*16").s("sawtooth").lpf(1000)` },
            { name: "Acid", code: `note("c2 c3 eb2").s("sawtooth").lpf(sine.range(200,2000).fast(4))` }
        ],
        "MELODY": [
            { name: "Arp", code: `note("c3 e3 g3 b3").s("sawtooth")` },
            { name: "Pad", code: `note("c3 e3 g3").s("sawtooth").lpf(800).room(2)` }
        ],
        "FX": [
            { name: "Delay", code: `.delay(0.5)` },
            { name: "Reverb", code: `.room(0.8)` },
            { name: "LPF", code: `.lpf(1000)` }
        ],
        "VISUALS": [
            { name: "Scope", code: `.scope()`, hasParams: true, visual: 'scope' },
            { name: "Piano Roll", code: `.pianoroll()`, hasParams: true, visual: 'pianoroll' },
            { name: "Punchcard", code: `.punchcard()`, hasParams: true, visual: 'punchcard' },
            { name: "Spiral", code: `.spiral()`, hasParams: true, visual: 'spiral' },
            { name: "Pitchwheel", code: `.pitchwheel()`, hasParams: true, visual: 'pitchwheel' },
            { name: "Spectrum", code: `.spectrum()`, hasParams: true, visual: 'spectrum' }
        ]
    };

    let selectedItem = null;
    let currentParamBuilder = null;

    function createParamBuilder(visual, container) {
        const params = visualParams[visual];
        if (!params) return null;

        const builder = document.createElement('div');
        builder.className = 'param-builder';
        builder.id = `param-${visual}`;

        let html = '';
        for (const [key, config] of Object.entries(params)) {
            html += `<div class="param-row">`;
            html += `<div class="param-label">${key}:</div>`;

            if (config.type === 'boolean') {
                html += `<select class="param-select" data-param="${key}">
                    <option value="false" ${!config.default ? 'selected' : ''}>false</option>
                    <option value="true" ${config.default ? 'selected' : ''}>true</option>
                </select>`;
            } else if (config.type === 'select') {
                html += `<select class="param-select" data-param="${key}">`;
                config.options.forEach(opt => {
                    html += `<option value="${opt}" ${opt === config.default ? 'selected' : ''}>${opt}</option>`;
                });
                html += `</select>`;
            } else if (config.type === 'color') {
                html += `<input type="text" class="param-input" data-param="${key}" value="${config.default}" placeholder="${config.default}">`;
            } else if (config.type === 'number') {
                const step = config.step || 1;
                const min = config.min !== undefined ? config.min : '';
                const max = config.max !== undefined ? config.max : '';
                html += `<input type="number" class="param-input" data-param="${key}" value="${config.default}" min="${min}" max="${max}" step="${step}">`;
            } else {
                html += `<input type="text" class="param-input" data-param="${key}" value="${config.default}">`;
            }

            html += `</div>`;
        }

        html += `<button class="btn-apply" onclick="applyVisualParams('${visual}')">✓ APPLY WITH OPTIONS</button>`;
        builder.innerHTML = html;
        
        return builder;
    }

    function applyVisualParams(visual) {
        const builder = document.getElementById(`param-${visual}`);
        const inputs = builder.querySelectorAll('[data-param]');
        
        const params = {};
        inputs.forEach(input => {
            const key = input.dataset.param;
            let value = input.value;
            
            if (input.tagName === 'SELECT' && (value === 'true' || value === 'false')) {
                value = value === 'true' ? 1 : 0;
            } else if (input.type === 'number') {
                value = parseFloat(value);
            } else if (value.startsWith('#') || value === 'white' || value === 'transparent') {
                value = `"${value}"`;
            } else if (isNaN(value) && value !== 'true' && value !== 'false') {
                value = `"${value}"`;
            }
            
            params[key] = value;
        });

        const paramStr = Object.entries(params)
            .map(([k, v]) => `${k}: ${v}`)
            .join(', ');
        
        const code = `.${visual}({ ${paramStr} })`;
        smartInsert(code);
        
        if (builder) builder.classList.remove('active');
    }

    function initLibrary() {
        const container = document.getElementById('library-container');
        for (const [category, items] of Object.entries(patterns)) {
            const details = document.createElement('details');
            details.open = (category === "KICKS" || category === "DRUMS");
            details.innerHTML = `<summary>${category}</summary>`;
            
            items.forEach((item, idx) => {
                const div = document.createElement('div');
                div.className = 'lib-item';
                
                let itemHTML = `
                    <div class="lib-name">${item.name}</div>
                    <div class="lib-code">${item.code.replace(/\n/g, ' ')}</div>
                    <button class="btn-add" onclick="addPattern('${category}', ${idx})">+ ADD</button>
                `;
                
                div.innerHTML = itemHTML;
                
                // Add param builder if visual
                if (item.hasParams && item.visual) {
                    const builder = createParamBuilder(item.visual, div);
                    if (builder) {
                        div.appendChild(builder);
                        
                        // Toggle builder on name click
                        const nameElem = div.querySelector('.lib-name');
                        nameElem.style.cursor = 'pointer';
                        nameElem.onclick = (e) => {
                            e.stopPropagation();
                            const allBuilders = document.querySelectorAll('.param-builder');
                            allBuilders.forEach(b => {
                                if (b !== builder) b.classList.remove('active');
                            });
                            builder.classList.toggle('active');
                        };
                    }
                }
                
                div.addEventListener('click', (e) => {
                    if (!e.target.classList.contains('btn-add') && 
                        !e.target.classList.contains('btn-apply') &&
                        !e.target.classList.contains('param-input') &&
                        !e.target.classList.contains('param-select') &&
                        e.target !== div.querySelector('.lib-name')) {
                        if (selectedItem) selectedItem.classList.remove('selected');
                        div.classList.add('selected');
                        selectedItem = div;
                    }
                });
                
                details.appendChild(div);
            });
            container.appendChild(details);
        }
    }

    function addPattern(category, idx) {
        const pattern = patterns[category][idx];
        smartInsert(pattern.code);
    }

    function smartInsert(snippet) {
        const textarea = document.getElementById('code-input');
        let code = textarea.value;
        
        snippet = snippet.trim();
        
        const lines = code.split('\n');
        const codeLines = lines.filter(l => l.trim() && !l.trim().startsWith('//'));
        const codeOnly = codeLines.join('\n');
        
        if (snippet.startsWith('.')) {
            for (let i = lines.length - 1; i >= 0; i--) {
                const trimmed = lines[i].trim();
                if (trimmed && !trimmed.startsWith('//') && !trimmed.startsWith(')') && trimmed !== 'stack(') {
                    lines[i] = lines[i].replace(/,\s*$/, '').trimEnd() + snippet;
                    textarea.value = lines.join('\n');
                    return;
                }
            }
        }
        
        const hasStack = /stack\s*\(/.test(codeOnly);
        
        if (!hasStack) {
            textarea.value = 'stack(\n  ' + snippet + '\n)';
        } else {
            let depth = 0;
            let stackStart = code.indexOf('stack(') + 6;
            let stackEnd = -1;
            
            for (let i = stackStart; i < code.length; i++) {
                if (code[i] === '(') depth++;
                if (code[i] === ')') {
                    if (depth === 0) {
                        stackEnd = i;
                        break;
                    }
                    depth--;
                }
            }
            
            if (stackEnd !== -1) {
                const before = code.substring(0, stackEnd).trimEnd();
                const after = code.substring(stackEnd);
                const lastChar = before.trim().slice(-1);
                const needsComma = lastChar !== '(' && lastChar !== ',';
                
                const formatted = snippet.split('\n').map((l, i) => i === 0 ? l : '  ' + l).join('\n');
                textarea.value = before + (needsComma ? ',\n  ' : '\n  ') + formatted + after;
            }
        }
        
        validateAndFix();
    }

    function validateAndFix() {
        const textarea = document.getElementById('code-input');
        let code = textarea.value;
        const statusDot = document.getElementById('status-dot');
        const statusText = document.getElementById('status-text');
        const errorPanel = document.getElementById('error-panel');
        
        statusDot.className = 'status-dot checking';
        statusText.textContent = 'FIXING...';
        statusText.style.color = 'var(--warning)';
        
        const errors = [];
        
        const stackCount = (code.match(/stack\s*\(/g) || []).length;
        if (stackCount > 1) {
            errors.push({ title: "NESTED STACK", desc: "Removed nested stack() calls" });
            code = code.replace(/stack\s*\(\s*stack\s*\(/g, 'stack(');
            code = code.replace(/\)\s*\)\s*$/g, ')');
        }
        
        const codeOnly = code.split('\n').filter(l => l.trim() && !l.trim().startsWith('//')).join('\n');
        const open = (codeOnly.match(/\(/g) || []).length;
        const close = (codeOnly.match(/\)/g) || []).length;
        
        if (open > close) {
            errors.push({ title: "MISSING CLOSING PARENS", desc: `Added ${open - close}` });
            code = code.trimEnd() + ')'.repeat(open - close);
        } else if (close > open) {
            errors.push({ title: "EXTRA CLOSING PARENS", desc: "Removed extras" });
            let toRemove = close - open;
            while (toRemove > 0 && code.trimEnd().endsWith(')')) {
                code = code.trimEnd().slice(0, -1);
                toRemove--;
            }
        }
        
        code = code.replace(/,+/g, ',');
        code = code.replace(/,\s*\)/g, ')');
        
        const quotes = (codeOnly.match(/"/g) || []).length;
        if (quotes % 2 !== 0) {
            errors.push({ title: "UNMATCHED QUOTES", desc: "Check strings" });
        }
        
        code = code.replace(/[\u200B-\u200D\uFEFF]/g, '');
        
        textarea.value = code;
        
        setTimeout(() => {
            if (errors.length === 0) {
                statusDot.className = 'status-dot valid';
                statusText.textContent = 'READY ✓';
                statusText.style.color = 'var(--success)';
                textarea.className = 'valid';
                errorPanel.className = 'error-panel';
            } else {
                statusDot.className = 'status-dot';
                statusText.textContent = 'FIXED';
                statusText.style.color = 'var(--coral)';
                textarea.className = '';
                errorPanel.className = 'error-panel show';
                errorPanel.innerHTML = `
                    <div class="error-list">
                        ${errors.map(e => `
                            <div class="error-item">
                                <div class="error-title">${e.title}</div>
                                <div class="error-desc">${e.desc}</div>
                            </div>
                        `).join('')}
                    </div>
                `;
            }
        }, 200);
    }

    initLibrary();
    generateSuggestions();
</script>
</body>
</html>
