<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Pitch Training</title>
    <style>
        * { box-sizing: border-box; }
        :root {
            --bg-dark: #000000;
            /* Updated to a deeper, richer gold/yellow background */
            --panel-bg: #e6c200; 
            --text-main: #1a1a1a;
            --text-sub: #444444;
            --progress-bg: #cbb000;
            --progress-fill: #2ecc71;
            --timer-color: #e74c3c;
            --accent-yellow: #ffff00; /* The "Reference" yellow */
        }

        body {
            font-family: 'Segoe UI', Roboto, Arial, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            margin: 0;
            display: flex;
            justify-content: center;
            padding: 20px;
        }

        .container { 
            width: 100%; 
            max-width: 500px; 
            background-color: var(--panel-bg);
            padding: 30px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            display: flex; 
            flex-direction: column; 
            align-items: center; 
        }

        h1 { margin-bottom: 5px; color: #000; }
        .subtitle { color: var(--text-sub); margin-bottom: 25px; font-size: 0.9em; }

        .controls {
            display: flex;
            gap: 15px;
            margin-bottom: 20px;
            width: 100%;
        }

        .control-group {
            flex: 1;
            display: flex;
            flex-direction: column;
        }

        label { font-size: 0.8em; font-weight: bold; margin-bottom: 5px; }

        input, button {
            padding: 12px;
            border-radius: 8px;
            border: none;
            font-size: 1rem;
        }

        button {
            cursor: pointer;
            background-color: #000;
            color: white;
            font-weight: bold;
            transition: transform 0.1s, background-color 0.2s;
            width: 100%;
        }

        button:active { transform: scale(0.98); }
        button:disabled { background-color: #666; cursor: not-allowed; }
        
        /* Highlight the test button to show it's a prerequisite */
        button.test-mode {
            background-color: #2980b9;
        }

        #status {
            margin-top: 15px;
            font-weight: bold;
            text-align: center;
            min-height: 1.2em;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>Pitch Trainer</h1>
    <p class="subtitle">Master your musical ear</p>

    <div class="controls">
        <div class="control-group">
            <label for="roundInput">ROUND</label>
            <input type="number" id="roundInput" value="1" min="1">
        </div>
        <div class="control-group">
            <label>&nbsp;</label>
            <button id="actionBtn">Test Round</button>
        </div>
    </div>

    <div id="status">Ready to test?</div>
</div>

<script>
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const actionBtn = document.getElementById('actionBtn');
    const roundInput = document.getElementById('roundInput');
    const status = document.getElementById('status');

    let isTestMode = true;

    // Frequencies for Round 1 (C4-B4) - Logic expands per round
    const getNotesForRound = (round) => {
        const baseFreqs = [261.63, 293.66, 329.63, 349.23, 392.00, 440.00, 493.88];
        // Just as an example, we return a set of notes based on the round
        return baseFreqs.slice(0, Math.min(round + 2, 7));
    };

    const playNote = (freq) => {
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'triangle';
        osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
        gain.gain.setTargetAtTime(0.5, audioCtx.currentTime, 0.05);
        gain.gain.setTargetAtTime(0, audioCtx.currentTime + 0.4, 0.05);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + 0.5);
    };

    const runTest = async () => {
        actionBtn.disabled = true;
        const notes = getNotesForRound(parseInt(roundInput.value));
        status.innerText = "Listening to round notes...";
        
        for (let i = 0; i < notes.length; i++) {
            playNote(notes[i]);
            await new Promise(r => setTimeout(r, 600));
        }

        status.innerText = "Test complete. Ready to start!";
        isTestMode = false;
        actionBtn.innerText = "Start Round";
        actionBtn.classList.remove('test-mode');
        actionBtn.disabled = false;
    };

    const startRound = () => {
        status.innerText = "Round Started! Good luck.";
        // Your actual game logic here
    };

    // Whenever the round input changes, force a re-test
    roundInput.addEventListener('input', () => {
        isTestMode = true;
        actionBtn.innerText = "Test Round";
        actionBtn.classList.add('test-mode');
        status.innerText = "Round changed. Please test new notes.";
    });

    actionBtn.addEventListener('click', () => {
        if (audioCtx.state === 'suspended') audioCtx.resume();
        
        if (isTestMode) {
            runTest();
        } else {
            startRound();
        }
    });
</script>

</body>
</html>
