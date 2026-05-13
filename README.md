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
            justify-content: center;
            padding: 20px;
        }

        .container { width: 100%; max-width: 500px; display: flex; flex-direction: column; align-items: center; }

        h1 { 
            color: #1a73e8; 
            font-size: 1.8rem; 
            text-align: center; 
            margin: 20px 0 10px 0; 
            width: 100%; 
        }

        .header-line {
            width: 100%;
            height: 1px;
            background: #ffffff;
            margin-bottom: 20px;
            opacity: 0.3;
        }

        .stats { display: flex; justify-content: center; gap: 30px; margin-bottom: 20px; }
        .stat-group { display: flex; flex-direction: column; align-items: center; }
        .stat-val { font-size: 5rem; font-weight: 800; line-height: 1; }
        .stat-label { font-size: 0.8rem; text-transform: uppercase; color: var(--text-sub); letter-spacing: 1px; }

        .game-panel {
            background-color: var(--panel-bg);
            padding: 25px;
            border-radius: 40px;
            display: flex;
            flex-direction: column;
            align-items: center;
            width: 100%;
        }

        .progress-container {
            width: 100%;
            height: 12px;
            background-color: var(--progress-bg);
            border-radius: 10px;
            margin-bottom: 10px;
            overflow: hidden;
        }
        .progress-bar { height: 100%; width: 0%; background-color: var(--progress-fill); transition: width 0.3s; }

        #timer-text {
            font-size: 1.2rem;
            font-weight: 800;
            color: var(--timer-color);
            margin-bottom: 10px;
            height: 1.5rem;
        }

        #play-btn, #replay-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 30px; 
            border-radius: 40px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            margin-bottom: 10px;
        }
        #replay-btn { background: #1a73e8; display: none; }

        .grid {
            display: flex;
            flex-wrap: wrap;
            justify-content: center;
            gap: 10px;
            margin-top: 10px;
            width: 100%;
        }

        .chord-btn {
            width: calc(25% - 10px); 
            aspect-ratio: 1 / 1;
            border-radius: 15px;
            border: none;
            cursor: pointer;
            /* BIGGER EMOJI SIZE HERE */
            font-size: 4.5rem; 
            display: flex;
            align-items: center;
            justify-content: center;
            transition: all 0.2s;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
            background-color: #dfe6e9; 
            color: transparent; 
            padding: 0;
            overflow: hidden;
            line-height: 1;
        }

        .chord-btn.active { color: white !important; }
        
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
        .chord-btn.active.rust { background-color: #d35400; }

        .chord-btn img {
            /* BIGGER OCTOPUS IMAGE HERE */
            width: 95%;
            height: 95%;
            object-fit: contain;
            display: none; 
            background-color: transparent !important;
            mix-blend-mode: multiply;
        }

        .chord-btn.active img { display: block !important; }

        #msg { font-weight: 600; color: #636e72; min-height: 1.5rem; text-align: center; margin-top: 5px; font-size: 0.9rem; }
    </style>
</head>
<body>

<div class="container">
    <h1>PRODIGIES-EGUCHI</h1>
    <div class="header-line"></div>

    <div class="stats">
        <div class="stat-group"><span id="streak" class="stat-val">0</span><span class="stat-label">Streak</span></div>
        <div class="stat-group"><span id="strikes" class="stat-val" style="color:#ff5252">0</span><span class="stat-label">Strikes</span></div>
    </div>

    <div class="game-panel">
        <div class="progress-container"><div id="progress-bar" class="progress-bar"></div></div>
        <div id="timer-text"></div>
        
        <button id="play-btn" onclick="startRound()">Start Level</button>
        <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
        <div id="msg">Practice Mode: Tap icons to learn</div>

        <div class="grid">
            <button class="chord-btn red" id="btn-red" onclick="handleInput('red')">🦞</button>
            <button class="chord-btn brown" id="btn-brown" onclick="handleInput('brown')">🐻</button>
            <button class="chord-btn pink" id="btn-pink" onclick="handleInput('pink')">🐷</button>
            <button class="chord-btn purple" id="btn-purple" onclick="handleInput('purple')">
                <img src="image_6.png" alt="Octopus">
            </button>
            
            <button class="chord-btn orange" id="btn-orange" onclick="handleInput('orange')">🦊</button>
            <button class="chord-btn yellow" id="btn-yellow" onclick="handleInput('yellow')">🐥</button>
            <button class="chord-btn green" id="btn-green" onclick="handleInput('green')">🐸</button>
            <button class="chord-btn teal" id="btn-teal" onclick="handleInput('teal')">🐬</button>

            <button class="chord-btn grey-note" id="btn-grey" onclick="handleInput('grey')">🐘</button>
            <button class="chord-btn darkorange" id="btn-darkorange" onclick="handleInput('darkorange')">🍊</button>
            <button class="chord-btn darkgreen" id="btn-darkgreen" onclick="handleInput('darkgreen')">🥝</button>
            <button class="chord-btn indigo" id="btn-indigo" onclick="handleInput('indigo')">🫐</button>

            <button class="chord-btn lavender" id="btn-lavender" onclick="handleInput('lavender')">🍇</button>
            <button class="chord-btn rust" id="btn-rust" onclick="handleInput('rust')">🍑</button>
        </div>
    </div>
</div>

<script>
    const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    const soundBuffers = {};
    
    const progression = [
        'red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey',
        'darkorange', 'darkgreen', 'indigo', 'lavender', 'rust'
    ];
    
    let activeCount = 2; 
    let streak = 0;
    let strikes = 0;
    let currentTarget = null;
    let isGameActive = false;
    let timeLeft = 10;
    let timerInterval = null;

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

    function replaySound() { 
        if (currentTarget) {
            playSound(currentTarget);
            resetTimer();
        }
    }

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

    function resetTimer() { startTimer(); }

    function stopTimer() {
        clearInterval(timerInterval);
        document.getElementById('timer-text').innerText = "";
    }

    function handleTimeout() {
        stopTimer();
        document.getElementById('msg').innerText = "Too slow! ⏰";
        processWrong();
    }

    function startRound() {
        isGameActive = true;
        document.getElementById('play-btn').style.display = "none";
        document.getElementById('replay-btn').style.display = "block";
        nextQuestion();
    }

    function nextQuestion() {
        const available = progression.slice(0, activeCount);
        currentTarget = available[Math.floor(Math.random() * available.length)];
        playSound(currentTarget);
        document.getElementById('msg').innerText = "Which one sang?";
        startTimer();
    }

    function handleInput(choice) {
        const btnId = (choice === 'grey') ? 'btn-grey' : `btn-${choice}`;
        const btn = document.getElementById(btnId);
        
        if (!btn.classList.contains('active')) return;

        if (!isGameActive) {
            playSound(choice);
            return;
        }

        if (choice === currentTarget) {
            stopTimer();
            streak++;
            document.getElementById('msg').innerText = "Correct! ✨";
            
            if (streak >= 20 && activeCount < progression.length) {
                activeCount++;
                streak = 0;
                updateUI();
                isGameActive = false;
                document.getElementById('play-btn').style.display = "block";
                document.getElementById('replay-btn').style.display = "none";
                document.getElementById('msg').innerText = "Level Up! Practice Mode.";
            } else {
                updateUI();
                setTimeout(nextQuestion, 1000);
            }
        } else {
            stopTimer();
            document.getElementById('msg').innerText = "Oops!";
            processWrong();
        }
    }

    function processWrong() {
        strikes++;
        streak = 0;
        updateUI();
        if (strikes >= 3) {
            alert("Game Over!");
            location.reload();
        } else {
            setTimeout(nextQuestion, 1500);
        }
    }

    function updateUI() {
        document.getElementById('streak').innerText = streak;
        document.getElementById('strikes').innerText = strikes;
        document.getElementById('progress-bar').style.width = (streak * 5) + "%";
        
        progression.forEach((color, i) => {
            const btnId = (color === 'grey') ? 'btn-grey' : `btn-${color}`;
            const btn = document.getElementById(btnId);
            if (i < activeCount) {
                btn.classList.add('active');
            } else {
                btn.classList.remove('active');
            }
        });
    }

    updateUI();
</script>
</body>
</html>
