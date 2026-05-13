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
            --timer-color: #e74c3c;
        }

        body {
            font-family: 'Segoe UI', Roboto, Arial, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            margin: 0;
            display: flex;
            flex-direction: column;
            align-items: center; 
            padding: 20px;
            min-height: 100vh;
        }

        .container { 
            width: 100%; 
            max-width: 500px; 
            display: flex; 
            flex-direction: column; 
            align-items: center; 
        }

        /* FIXED CENTERING FOR STATS */
        .stats { 
            display: flex; 
            justify-content: center; 
            align-items: center;
            gap: 20px; 
            margin: 40px auto 30px auto; 
            width: 100%;
            max-width: 400px;
        }
        
        .stat-group { 
            flex: 1; /* Gives both sides equal weight to force true center */
            display: flex; 
            flex-direction: column; 
            align-items: center;
            text-align: center;
        }
        
        .stat-val { font-size: 5.5rem; font-weight: 800; line-height: 1; margin: 0; }
        .stat-label { font-size: 0.85rem; text-transform: uppercase; color: var(--text-sub); letter-spacing: 2px; margin-top: 8px; }

        /* GAME PANEL */
        .game-panel {
            background-color: var(--panel-bg);
            padding: 25px;
            border-radius: 40px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
            min-height: 580px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }

        .mode-tag {
            background: #dfe6e9;
            color: #636e72;
            padding: 4px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 700;
            text-transform: uppercase;
            margin-bottom: 15px;
        }
        .mode-tag.game-on { background: #ffeaa7; color: #d35400; }

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

        .progress-container { width: 100%; height: 12px; background-color: var(--progress-bg); border-radius: 10px; margin-bottom: 15px; overflow: hidden; }
        .progress-bar { height: 100%; width: 0%; background-color: var(--progress-fill); transition: width 0.3s; }

        #timer-text { font-size: 1.2rem; font-weight: 800; color: var(--timer-color); margin-bottom: 10px; height: 1.5rem; }

        #play-btn, #replay-btn {
            background: #2ecc71; color: white; border: none; padding: 12px 35px; border-radius: 40px;
            font-size: 1.1rem; font-weight: 700; cursor: pointer; margin-bottom: 15px;
            box-shadow: 0 4px 15px rgba(46, 204, 113, 0.3);
        }
        #replay-btn { background: #1a73e8; display: none; box-shadow: 0 4px 15px rgba(26, 115, 232, 0.3); }

        .grid { display: flex; flex-wrap: wrap; justify-content: center; gap: 10px; margin-top: 10px; width: 100%; }

        .chord-btn {
            width: calc(25% - 10px); aspect-ratio: 1 / 1; border-radius: 15px; border: none;
            cursor: pointer; font-size: 2.8rem; display: flex; align-items: center; justify-content: center;
            transition: background-color 0.2s, transform 0.1s; box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            background-color: #dfe6e9; padding: 0; position: relative;
        }
        .chord-btn:active { transform: scale(0.92); }

        .chord-btn span, .chord-btn img {
            width: 75%; height: 75%; display: flex; align-items: center; justify-content: center;
            object-fit: contain; opacity: 0; transition: opacity 0.2s;
        }

        .chord-btn.active span, .chord-btn.active img { opacity: 1; }

        /* Colors */
        .chord-btn.active.red { background-color: #ff5252; }
        .chord-btn.active.brown { background-color: #8d6e63; }
        .chord-btn.active.pink { background-color: #f48fb1; }
        .chord-btn.active.purple { background-color: #ba68c8; }
        .chord-btn.active.orange { background-color: #ffb74d; }
        .chord-btn.active.yellow { background-color: #fff176; }
        .chord-btn.active.green { background-color: #81c784; }
        .chord-btn.active.teal { background-color: #4db6ac; }
        .chord-btn.active.grey-note { background-color: #b0bec5; }
        .chord-btn.active.darkorange { background-color: #e67e22; }
        .chord-btn.active.darkgreen { background-color: #1b5e20; }
        .chord-btn.active.indigo { background-color: #3f51b5; }
        .chord-btn.active.lavender { background-color: #9b59b6; }
        .chord-btn.active.lightyellow { background-color: #fafad2; }

        .chord-btn img { mix-blend-mode: multiply; }
        #msg { font-weight: 600; color: #636e72; min-height: 1.5rem; text-align: center; margin-top: 10px; font-size: 0.95rem; }

        @media (max-width: 400px) {
            .stat-val { font-size: 4.5rem; }
            .chord-btn { font-size: 2.2rem; }
            .game-panel { min-height: 520px; padding: 20px; }
        }
    </style>
</head>
<body>

<div class="container">
    <!-- Centered Stats Only (Headers removed to fix duplication) -->
    <div class="stats">
        <div class="stat-group">
            <span id="streak" class="stat-val">0</span>
            <span class="stat-label">Streak</span>
        </div>
        <div class="stat-group">
            <span id="strikes" class="stat-val" style="color:#ff5252">0</span>
            <span class="stat-label">Strikes</span>
        </div>
    </div>

    <div class="game-panel">
        <div id="mode-label" class="mode-tag">Test Mode</div>
        
        <div class="level-select-container" id="selector-box">
            Start at Level: 
            <select id="level-select" onchange="changeStartingLevel()">
                <!-- JS generated -->
            </select>
        </div>

        <div class="progress-container"><div id="progress-bar" class="progress-bar"></div></div>
        <div id="timer-text"></div>
        
        <button id="play-btn" onclick="startRound()">Start Game</button>
        <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
        <div id="msg">Explore all sounds before playing!</div>

        <div class="grid" id="button-grid">
            <button class="chord-btn active red" id="btn-red" onclick="handleInput('red')"><span>🦞</span></button>
            <button class="chord-btn active brown" id="btn-brown" onclick="handleInput('brown')"><span>🐻</span></button>
            <button class="chord-btn active pink" id="btn-pink" onclick="handleInput('pink')"><span>🐷</span></button>
            <button class="chord-btn active purple" id="btn-purple" onclick="handleInput('purple')"><img src="image_6.png" alt="Octopus"></button>
            <button class="chord-btn active orange" id="btn-orange" onclick="handleInput('orange')"><span>🦊</span></button>
            <button class="chord-btn active yellow" id="btn-yellow" onclick="handleInput('yellow')"><span>🐥</span></button>
            <button class="chord-btn active green" id="btn-green" onclick="handleInput('green')"><span>🐸</span></button>
            <button class="chord-btn active teal" id="btn-teal" onclick="handleInput('teal')"><span>🐬</span></button>
            <button class="chord-btn active grey-note" id="btn-grey" onclick="handleInput('grey')"><span>🐘</span></button>
            <button class="chord-btn active darkorange" id="btn-darkorange" onclick="handleInput('darkorange')"><span>🍊</span></button>
            <button class="chord-btn active darkgreen" id="btn-darkgreen" onclick="handleInput('darkgreen')"><span>🥝</span></button>
            <button class="chord-btn active indigo" id="btn-indigo" onclick="handleInput('indigo')"><span>🫐</span></button>
            <button class="chord-btn active lavender" id="btn-lavender" onclick="handleInput('lavender')"><span>🍇</span></button>
            <button class="chord-btn active lightyellow" id="btn-lightyellow" onclick="handleInput('lightyellow')"><span>🍍</span></button>
        </div>
    </div>
</div>

<script>
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const soundBuffers = {};
    const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey', 'darkorange', 'darkgreen', 'indigo', 'lavender', 'lightyellow'];
    
    let gameLevel = 1; 
    let streak = 0;
    let strikes = 0;
    let currentTarget = null;
    let isGameActive = false;
    let timeLeft = 10;
    let timerInterval = null;

    const selector = document.getElementById('level-select');
    progression.forEach((_, i) => {
        let opt = document.createElement('option');
        opt.value = i + 1;
        opt.innerHTML = `Level ${i + 1}`;
        selector.appendChild(opt);
    });

    function changeStartingLevel() {
        if (!isGameActive) {
            gameLevel = parseInt(selector.value);
            updateUI();
        }
    }

    async function loadSound(name) {
        try {
            const response = await fetch(`${name}.wav`);
            const arrayBuffer = await response.arrayBuffer();
            soundBuffers[name] = await audioCtx.decodeAudioData(arrayBuffer);
        } catch (e) { console.log("Sound failed: " + name); }
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

    function startRound() {
        isGameActive = true;
        streak = 0;
        document.getElementById('play-btn').style.display = "none";
        document.getElementById('replay-btn').style.display = "block";
        document.getElementById('selector-box').style.display = "none";
        document.getElementById('mode-label').innerText = "Game Mode";
        document.getElementById('mode-label').classList.add('game-on');
        updateUI(); 
        nextQuestion();
    }

    function nextQuestion() {
        const available = progression.slice(0, gameLevel);
        currentTarget = available[Math.floor(Math.random() * available.length)];
        playSound(currentTarget);
        document.getElementById('msg').innerText = "Which one was that?";
        startTimer();
    }

    function handleInput(choice) {
        const btnId = choice === 'grey' ? 'btn-grey' : `btn-${choice}`;
        const btn = document.getElementById(btnId);
        if (!btn.classList.contains('active')) return;

        if (!isGameActive) {
            playSound(choice);
            document.getElementById('msg').innerText = "Testing: " + choice;
            return;
        }

        if (choice === currentTarget) {
            stopTimer();
            streak++;
            document.getElementById('msg').innerText = "Correct! ✨";
            
            if (streak >= 20) {
                if (gameLevel < progression.length) {
                    gameLevel++;
                    streak = 0;
                    document.getElementById('msg').innerText = "LEVEL UP!";
                    updateUI();
                    setTimeout(nextQuestion, 1500); 
                } else {
                    document.getElementById('msg').innerText = "MASTERED!";
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
        progression.forEach((color, i) => {
            const btnId = color === 'grey' ? 'btn-grey' : `btn-${color}`;
            const btn = document.getElementById(btnId);
            if (!isGameActive || i < gameLevel) btn.classList.add('active');
            else btn.classList.remove('active');
        });
    }
    updateUI();
</script>
</body>
</html>
