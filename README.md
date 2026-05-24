<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CHROMACHORDS - Bells Edition</title>
    <style>
        * { box-sizing: border-box; }
        :root {
            --bg-dark: #000000;
            --panel-bg: #f0f2f5;
            --text-main: #ffffff;
            --text-sub: #a0a0a0;
            --progress-bg: #dfe6e9;
            --progress-fill: #2ecc71;
            --timer-color: #e74c3c;
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

        .container { width: 100%; max-width: 600px; display: flex; flex-direction: column; align-items: center; }

        h1 { color: #1a73e8; font-size: 2.2rem; text-align: center; margin: 10px 0 5px 0; width: 100%; letter-spacing: 2px; }

        .header-line { width: 100%; height: 2px; background: #1a73e8; margin-bottom: 20px; opacity: 0.5; width: 80%; }

        .stats { display: flex; justify-content: center; gap: 30px; margin-bottom: 15px; }
        .stat-group { display: flex; flex-direction: column; align-items: center; }
        .stat-val { font-size: 2.5rem; font-weight: 800; line-height: 1; }
        .stat-label { font-size: 0.8rem; text-transform: uppercase; color: var(--text-sub); letter-spacing: 1px; }

        .game-panel {
            background-color: var(--panel-bg);
            padding: 25px 25px 40px 25px;
            border-radius: 40px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            height: auto;
        }

        .mode-tag {
            background: #dfe6e9;
            color: #636e72;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            margin-bottom: 10px;
        }
        .mode-tag.game-on { background: #ffeaa7; color: #d35400; }
        .mode-tag.test-round { background: #a29bfe; color: #ffffff; }

        .level-select-container { margin-bottom: 15px; color: #636e72; font-weight: 700; font-size: 0.9rem; }
        #level-select { 
            padding: 5px 10px; 
            border-radius: 10px; 
            border: 2px solid #dfe6e9; 
            background: white;
            font-weight: 700;
            color: #1a73e8;
            cursor: pointer;
        }

        .progress-container { width: 100%; height: 12px; background-color: var(--progress-bg); border-radius: 10px; margin-bottom: 5px; overflow: hidden; }
        .progress-bar { height: 100%; width: 0%; background-color: var(--progress-fill); transition: width 0.3s; }

        #timer-text { font-size: 1.2rem; font-weight: 800; color: var(--timer-color); margin-bottom: 5px; height: 1.5rem; }

        #play-btn, #replay-btn, #begin-round-btn {
            background: #2ecc71; color: white; border: none; padding: 12px 30px; border-radius: 40px;
            font-size: 1rem; font-weight: 700; cursor: pointer; margin-bottom: 10px;
            box-shadow: 0 4px 15px rgba(46, 204, 113, 0.3);
        }
        #replay-btn { background: #1a73e8; display: none; box-shadow: 0 4px 15px rgba(26, 115, 232, 0.3); }
        #begin-round-btn { background: #e67e22; display: none; box-shadow: 0 4px 15px rgba(230, 126, 34, 0.3); }

        .grid { display: flex; flex-wrap: wrap; justify-content: center; gap: 8px; margin-top: 5px; width: 100%; }

        .chord-btn {
            width: calc(20% - 8px); aspect-ratio: 1 / 1; border-radius: 15px; border: none;
            cursor: pointer; font-size: 1.5rem; display: flex; flex-direction: column; align-items: center; justify-content: center;
            transition: background-color 0.2s; box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            background-color: #dfe6e9; padding: 0; position: relative;
            color: #ffffff; font-weight: bold;
        }

        .chord-btn span.bell-emoji {
            font-size: 1.8rem; line-height: 1; opacity: 0; transition: opacity 0.2s;
        }

        .chord-btn span.note-label {
            font-size: 0.75rem; display: block; margin-top: 2px; text-shadow: 1px 1px 2px rgba(0,0,0,0.6); opacity: 0; transition: opacity 0.2s;
        }

        .chord-btn.active span.bell-emoji, .chord-btn.active span.note-label { opacity: 1; }

        /* Prodigies Desaturated / Base Colors matched to notes */
        .chord-btn.active.purple-3 { background-color: #9c27b0; }
        .chord-btn.active.pink-3   { background-color: #e91e63; }
        .chord-btn.active.red-4    { background-color: #f44336; }
        .chord-btn.active.orange-4 { background-color: #ff9800; }
        .chord-btn.active.yellow-4 { background-color: #ffeb3b; color: #000 !important; }
        .chord-btn.active.yellow-4 span.note-label { text-shadow: none; }
        .chord-btn.active.green-4  { background-color: #4caf50; }
        .chord-btn.active.teal-4   { background-color: #009688; }
        .chord-btn.active.purple-4 { background-color: #673ab7; }
        .chord-btn.active.pink-4   { background-color: #ff4081; }
        
        /* High Octave variants (lighter or styled shifts for distinct tracking) */
        .chord-btn.active.red-5    { background-color: #ff7961; }
        .chord-btn.active.orange-5 { background-color: #ffc947; color: #000 !important; }
        .chord-btn.active.orange-5 span.note-label { text-shadow: none; }
        .chord-btn.active.yellow-5 { background-color: #ffff72; color: #000 !important; }
        .chord-btn.active.yellow-5 span.note-label { text-shadow: none; }
        .chord-btn.active.green-5  { background-color: #80e27e; color: #000 !important; }
        .chord-btn.active.green-5 span.note-label { text-shadow: none; }
        .chord-btn.active.teal-5   { background-color: #52c7b8; }
        
        /* Sharp notes (Greyscale/Accent shifts) */
        .chord-btn.active.sharp-grey { background-color: #78909c; }

        #msg { font-weight: 600; color: #636e72; min-height: 1.5rem; text-align: center; margin-top: 5px; font-size: 0.95rem; }

        @media (max-width: 480px) {
            .chord-btn { font-size: 1.1rem; }
            .chord-btn span.bell-emoji { font-size: 1.3rem; }
            .chord-btn span.note-label { font-size: 0.65rem; }
        }
    </style>
</head>
<body>
<div class="container">
    <h1>CHROMACHORDS</h1>
    <div class="header-line"></div>
    
    <div class="stats">
        <div class="stat-group"><span id="streak" class="stat-val">0</span><span class="stat-label">Streak</span></div>
        <div class="stat-group"><span id="strikes" class="stat-val" style="color:#ff5252">0</span><span class="stat-label">Strikes</span></div>
    </div>

    <div class="game-panel">
        <div id="mode-label" class="mode-tag">Test Mode</div>
        
        <div class="level-select-container" id="selector-box">
            Start at Level: 
            <select id="level-select" onchange="changeStartingLevel()">
                <!-- Generated by JS -->
            </select>
        </div>

        <div class="progress-container"><div id="progress-bar" class="progress-bar"></div></div>
        <div id="timer-text"></div>
        
        <button id="play-btn" onclick="prepareRound()">Start Game</button>
        <button id="begin-round-btn" onclick="startActualGame()">Begin Round</button>
        <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
        <div id="msg">Explore all sounds before playing!</div>

        <div class="grid" id="button-grid">
            <!-- Dynamic Injection to match A3-E5 progression perfectly -->
        </div>
    </div>
</div>

<script>
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const soundBuffers = {};
    
    // Complete structured array mapping structural audio names, labels, and classes
    const progression = [
        { id: "A3", label: "A3", style: "purple-3" },
        { id: "Bb3", label: "A♯3", style: "sharp-grey" },
        { id: "B3", label: "B3", style: "pink-3" },
        { id: "C4", label: "C4", style: "red-4" },
        { id: "Db4", label: "C♯4", style: "sharp-grey" },
        { id: "D4", label: "D4", style: "orange-4" },
        { id: "Eb4", label: "D♯4", style: "sharp-grey" },
        { id: "E4", label: "E4", style: "yellow-4" },
        { id: "F4", label: "F4", style: "green-4" },
        { id: "Gb4", label: "F♯4", style: "sharp-grey" },
        { id: "G4", label: "G4", style: "teal-4" },
        { id: "Ab4", label: "G♯4", style: "sharp-grey" },
        { id: "A4", label: "A4", style: "purple-4" },
        { id: "Bb4", label: "A♯4", style: "sharp-grey" },
        { id: "B4", label: "B4", style: "pink-4" },
        { id: "C5", label: "C5", style: "red-5" },
        { id: "Db5", label: "C♯5", style: "sharp-grey" },
        { id: "D5", label: "D5", style: "orange-5" },
        { id: "Eb5", label: "D♯5", style: "sharp-grey" },
        { id: "E5", label: "E5", style: "yellow-5" }
    ];
    
    let gameLevel = 1; 
    let streak = 0;
    let strikes = 0;
    let currentTarget = null;
    let isGameActive = false;
    let isTestRound = false; 
    let timeLeft = 10;
    let timerInterval = null;

    // Render Grid Elements Dynamic Setup
    const gridContainer = document.getElementById('button-grid');
    progression.forEach((note) => {
        const btn = document.createElement('button');
        btn.className = `chord-btn active ${note.style}`;
        btn.id = `btn-${note.id}`;
        btn.setAttribute('onclick', `handleInput('${note.id}')`);
        
        btn.innerHTML = `<span class="bell-emoji">🛎️</span><span class="note-label">${note.label}</span>`;
        gridContainer.appendChild(btn);
    });

    const selector = document.getElementById('level-select');
    progression.forEach((note, i) => {
        let opt = document.createElement('option');
        opt.value = i + 1;
        opt.innerHTML = `Level ${i + 1} (${note.label})`;
        selector.appendChild(opt);
    });

    function changeStartingLevel() {
        if (!isGameActive) {
            gameLevel = parseInt(selector.value);
            updateUI();
        }
    }

    async function loadSound(noteObj) {
        try {
            const response = await fetch(`${noteObj.id}.wav`);
            const arrayBuffer = await response.arrayBuffer();
            soundBuffers[noteObj.id] = await audioCtx.decodeAudioData(arrayBuffer);
        } catch (e) { console.log("Sound failed: " + noteObj.id); }
    }
    progression.forEach(loadSound);

    function playSound(name) {
        if (audioCtx.state === 'suspended') audioCtx.resume();
        if (!soundBuffers[name]) return;
        const source = audioCtx.createBufferSource();
        source.buffer = soundBuffers[name];
        source.connect(audioCtx.destination);
        source.start(0);
    }

    function replaySound() { if (currentTarget) { playSound(currentTarget); startTimer(); } }

    function startTimer() {
        if (isTestRound) return; 
        clearInterval(timerInterval);
        timeLeft = 10;
        document.getElementById('timer-text').innerText = `Time: ${timeLeft}s`;
        timerInterval = setInterval(() => {
            timeLeft--;
            document.getElementById('timer-text').innerText = `Time: ${timeLeft}s`;
            if (timeLeft <= 0) handleTimeout();
        }, 1000);
    }

    function stopTimer() { clearInterval(timerInterval); document.getElementById('timer-text').innerText = ""; }

    function handleTimeout() { stopTimer(); document.getElementById('msg').innerText = "Too slow! ⏰"; processWrong(); }

    function prepareRound() {
        isGameActive = true;
        isTestRound = true;
        streak = 0;
        
        document.getElementById('play-btn').style.display = "none";
        document.getElementById('replay-btn').style.display = "none"; 
        document.getElementById('begin-round-btn').style.display = "block";
        document.getElementById('selector-box').style.display = "none";
        
        document.getElementById('mode-label').innerText = "Test Round: Try the new sound!";
        document.getElementById('mode-label').className = "mode-tag test-round";
        
        updateUI();
        
        const newNote = progression[gameLevel - 1];
        document.getElementById('msg').innerText = `Level ${gameLevel}: Practice playing ${newNote.label}!`;
    }

    function startActualGame() {
        isTestRound = false;
        document.getElementById('begin-round-btn').style.display = "none";
        document.getElementById('replay-btn').style.display = "block";
        document.getElementById('mode-label').innerText = "Game Mode";
        document.getElementById('mode-label').className = "mode-tag game-on";
        
        nextQuestion();
    }

    function nextQuestion() {
        const available = progression.slice(0, gameLevel);
        const choiceObj = available[Math.floor(Math.random() * available.length)];
        currentTarget = choiceObj.id;
        playSound(currentTarget);
        document.getElementById('msg').innerText = "Which note was that?";
        startTimer();
    }

    function handleInput(choice) {
        const btn = document.getElementById(`btn-${choice}`);
        if (!btn.classList.contains('active')) return;

        if (!isGameActive || isTestRound) {
            playSound(choice);
            if(isTestRound) {
                const noteItem = progression.find(p => p.id === choice);
                document.getElementById('msg').innerText = "Practice: " + noteItem.label;
            }
            return;
        }

        if (choice === currentTarget) {
            stopTimer();
            streak++;
            document.getElementById('msg').innerText = "Correct! ✨";
            
            if (streak >= 20) {
                if (gameLevel < progression.length) {
                    gameLevel++;
                    document.getElementById('msg').innerText = "LEVEL UP! Prepare for a new bell sound...";
                    setTimeout(prepareRound, 1500);
                } else {
                    document.getElementById('msg').innerText = "MASTERED! You finished all levels!";
                    isGameActive = false;
                    setTimeout(() => location.reload(), 3000);
                }
            } else {
                updateUI();
                setTimeout(nextQuestion, 800);
            }
        } else {
            stopTimer();
            processWrong();
        }
    }

    function processWrong() {
        strikes++; streak = 0; updateUI();
        if (strikes >= 3) { 
            document.getElementById('msg').innerText = "GAME OVER";
            setTimeout(() => location.reload(), 2000); 
        }
        else { setTimeout(nextQuestion, 1500); }
    }

    function updateUI() {
        document.getElementById('streak').innerText = streak;
        document.getElementById('strikes').innerText = strikes;
        document.getElementById('progress-bar').style.width = (streak * 5) + "%";
        progression.forEach((note, i) => {
            const btn = document.getElementById(`btn-${note.id}`);
            if (!isGameActive || i < gameLevel) btn.classList.add('active');
            else btn.classList.remove('active');
        });
    }
    updateUI();
</script>
</body>
</html>
