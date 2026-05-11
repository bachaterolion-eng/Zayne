
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Pitch Training</title>
    <style>
        * { box-sizing: border-box; }

        :root {
            --bg-dark: #000000;
            --panel-bg: #f0f2f5;
            --text-main: #ffffff;
            --text-sub: #a0a0a0;
            --progress-bg: #dfe6e9;
            --progress-fill: #2ecc71;
            /* Simple shades for visibility within the colored squares */
            --shade-invisible: rgba(0,0,0,0);
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .container {
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        h1 {
            color: #1a73e8;
            font-size: 1.8rem;
            font-weight: 700;
            margin: 20px 0;
            padding: 0;
            text-align: center;
            width: 100%;
        }

        .stats { 
            margin-bottom: 20px; 
            display: flex; 
            justify-content: center; 
            gap: 20px; 
            width: 100%;
        }
        
        .stat-group { 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
            width: 120px;
        }
        
        .stat-val { display: block; font-size: 5.5rem; font-weight: 700; color: #ffffff; line-height: 1; }
        .stat-label { font-size: 0.85rem; text-transform: uppercase; letter-spacing: 2px; color: var(--text-sub); }
        .strike-val { color: #ff5252; }

        .game-panel {
            background-color: var(--panel-bg);
            padding: 30px;
            border-radius: 40px;
            display: inline-flex;
            flex-direction: column;
            align-items: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            width: auto;
            max-width: fit-content;
        }

        .progress-container {
            width: 100%;
            min-width: 250px;
            height: 10px;
            background-color: var(--progress-bg);
            border-radius: 10px;
            margin-bottom: 25px;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            width: 0%;
            background-color: var(--progress-fill);
            transition: width 0.4s ease;
        }

        #play-btn, #replay-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 45px; 
            border-radius: 40px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.2s;
        }

        #replay-btn {
            background: #1a73e8;
            padding: 8px 25px;
            font-size: 0.9rem;
            margin-top: 10px;
            display: none; 
        }

        #replay-btn:hover { background: #1557b0; }

        .grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px; 
            margin-top: 25px;
        }

        .chord-btn {
            width: 100px;
            height: 100px;
            border-radius: 20px; 
            cursor: pointer;
            transition: transform 0.1s, opacity 0.3s;
            border: none;
            box-shadow: 0 4px 10px rgba(0,0,0,0.15);
            display: flex;
            align-items: center;
            justify-content: center;
            padding: 15px;
            /* Emojis aren't used anymore, so this isn't necessary, but left in. */
            font-size: 50px; 
            line-height: 1;
        }

        /* SVG styling for shapes to adopt button hue */
        .chord-btn svg {
            width: 100%;
            height: 100%;
            /* Sets fill to the hue of the button background */
            fill: currentColor; 
        }

        .chord-btn.locked {
            cursor: not-allowed;
            background-color: rgba(0, 0, 0, 0.05) !important;
            box-shadow: inset 0 2px 8px rgba(0,0,0,0.05);
            opacity: 1;
            /* Shape adopts the shadow hue when locked */
            color: rgba(0, 0, 0, 0.1); 
        }

        .red    { background-color: #ff5252; color: #ff5252; }
        .brown  { background-color: #8d6e63; color: #8d6e63; }
        .pink   { background-color: #f48fb1; color: #f48fb1; }
        .purple { background-color: #ba68c8; color: #ba68c8; }
        .orange { background-color: #ffb74d; color: #ffb74d; }
        .yellow { background-color: #fff176; color: #fff176; }
        .green  { background-color: #81c784; color: #81c784; }
        .teal   { background-color: #4db6ac; color: #4db6ac; }
        .grey   { background-color: #b0bec5; color: #b0bec5; }

        #msg { margin-top: 15px; font-size: 1rem; font-weight: 600; color: #636e72; min-height: 1.2rem; text-align: center;}
        #timer-display { font-weight: 700; color: #e74c3c; margin-top: 5px; font-size: 1.1rem; min-height: 1.2rem;}
    </style>
</head>
<body>

    <div class="container">
        <h1>PRODIGIES-EGUCHI</h1>

        <div class="stats">
            <div class="stat-group">
                <span id="streak" class="stat-val">0</span>
                <span class="stat-label">Streak</span>
            </div>
            <div class="stat-group">
                <span id="strikes" class="stat-val strike-val">0</span>
                <span class="stat-label">Strikes</span>
            </div>
        </div>

        <div class="game-panel">
            <div class="progress-container">
                <div id="progress-bar" class="progress-bar"></div>
            </div>

            <button id="play-btn" onclick="startRound()">Start Level</button>
            <div id="msg">Practice Mode: Tap the squares to learn them</div>
            <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
            <div id="timer-display"></div>

            <div class="grid">
                <button class="chord-btn red" id="btn-red" onclick="handleInput('red')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn brown locked" id="btn-brown" onclick="handleInput('brown')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn pink locked" id="btn-pink" onclick="handleInput('pink')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn purple locked" id="btn-purple" onclick="handleInput('purple')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn orange locked" id="btn-orange" onclick="handleInput('orange')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn yellow locked" id="btn-yellow" onclick="handleInput('yellow')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn green locked" id="btn-green" onclick="handleInput('green')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn teal locked" id="btn-teal" onclick="handleInput('teal')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
                <button class="chord-btn grey locked" id="btn-grey" onclick="handleInput('grey')">
                    <svg viewBox="0 0 512 512"><path d="M432.1 369.3c16 8 32 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1c16.1 8 32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1s32.1 16.1 48.1 24.1z"/></svg>
                </button>
            </div>
        </div>
    </div>

    <script>
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        const soundBuffers = {};
        const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey'];
        
        let activeCount = 2; 
        let streak = 0;
        let strikes = 0;
        let currentTarget = null;
        const streakGoal = 10;
        
        let timeLeft = 10;
        let timerInterval = null;
        let isGameActive = false;
        let canPractice = true;

        async function loadSound(name) {
            try {
                const response = await fetch(`${name}.wav`); 
                const arrayBuffer = await response.arrayBuffer();
                soundBuffers[name] = await audioCtx.decodeAudioData(arrayBuffer);
            } catch (e) { console.error(e); }
        }
        progression.forEach(name => loadSound(name));

        function playSound(name) {
            if (audioCtx.state === 'suspended') audioCtx.resume();
            if (!soundBuffers[name]) return;
            const source = audioCtx.createBufferSource();
            source.buffer = soundBuffers[name];
            source.connect(audioCtx.destination);
            source.start(0);
        }

        function replaySound() {
            if (currentTarget) playSound(currentTarget);
        }

        function startTimer() {
            clearInterval(timerInterval);
            timeLeft = 10;
            updateTimerUI();
            timerInterval = setInterval(() => {
                timeLeft--;
                updateTimerUI();
                if (timeLeft <= 0) {
                    stopTimer();
                    handleMistake("Time's up!");
                }
            }, 1000);
        }

        function stopTimer() {
            clearInterval(timerInterval);
            document.getElementById('timer-display').innerText = "";
        }

        function updateTimerUI() {
            document.getElementById('timer-display').innerText = timeLeft > 0 ? `Time: ${timeLeft}s` : "";
        }

        function handleMistake(message) {
            strikes++;
            streak = 0;
            currentTarget = null;
            isGameActive = false;
            document.getElementById('replay-btn').style.display = "none";
            
            if (strikes >= 3) {
                alert("3 Strikes! Back to Level 1.");
                strikes = 0;
                activeCount = 2;
                enterPracticeMode("Back to basics. Practice then start.");
            } else {
                document.getElementById('msg').innerText = message;
                document.getElementById('msg').style.color = "#e74c3c";
                setTimeout(nextQuestion, 1500);
            }
            updateUI();
        }

        function enterPracticeMode(message) {
            isGameActive = false;
            canPractice = true;
            document.getElementById('play-btn').style.display = "block";
            document.getElementById('replay-btn').style.display = "none";
            document.getElementById('msg').innerText = message;
            document.getElementById('msg').style.color = "#636e72";
            stopTimer();
        }

        function startRound() {
            canPractice = false;
            document.getElementById('play-btn').style.display = "none";
            nextQuestion();
        }

        function nextQuestion() {
            isGameActive = true;
            const available = progression.slice(0, activeCount);
            currentTarget = available[Math.floor(Math.random() * available.length)];
            
            playSound(currentTarget);
            document.getElementById('msg').innerText = "Identify the sound...";
            document.getElementById('msg').style.color = "#636e72";
            document.getElementById('replay-btn').style.display = "block";
            startTimer();
        }

        function handleInput(choice) {
            const btn = document.getElementById(`btn-${choice}`);
            if (btn && btn.classList.contains('locked')) return;

            if (canPractice) {
                playSound(choice);
                return;
            }

            if (isGameActive && currentTarget) {
                checkAnswer(choice);
            }
        }

        function checkAnswer(choice) {
            if (choice === currentTarget) {
                stopTimer();
                isGameActive = false;
                document.getElementById('replay-btn').style.display = "none";
                streak++;
                
                document.getElementById('msg').innerText = "Correct! ✨";
                document.getElementById('msg').style.color = "#2ecc71";
                
                if (streak >= streakGoal && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                    updateUI();
                    enterPracticeMode("New square unlocked! Practice then start.");
                } else {
                    updateUI();
                    setTimeout(nextQuestion, 1000);
                }
            } else {
                stopTimer();
                handleMistake("Wrong choice!");
            }
        }

        function updateUI() {
            document.getElementById('streak').innerText = streak;
            document.getElementById('strikes').innerText = strikes;
            document.getElementById('progress-bar').style.width = (streak / streakGoal * 100) + "%";
            
            progression.forEach((color, index) => {
                const btn = document.getElementById(`btn-${color}`);
                if (btn) {
                    if (index < activeCount) {
                        btn.classList.remove('locked');
                    } else {
                        btn.classList.add('locked');
                    }
                }
            });
        }
    </script>
</body>
</html>

```
