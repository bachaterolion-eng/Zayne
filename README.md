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

        h1 { color: #1a73e8; font-size: 1.8rem; text-align: center; margin-bottom: 10px; width: 100%; }

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
            margin-bottom: 20px;
            overflow: hidden;
        }
        .progress-bar { height: 100%; width: 0%; background-color: var(--progress-fill); transition: width 0.3s; }

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
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 12px;
            margin-top: 20px;
        }

        .chord-btn {
            width: 95px;
            height: 95px;
            border-radius: 20px;
            border: none;
            cursor: pointer;
            font-size: 3.2rem;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.1s;
            box-shadow: 0 4px 10px rgba(0,0,0,0.1);
        }

        /* Color Assignments */
        .red { background-color: #ff5252; }
        .brown { background-color: #8d6e63; }
        .pink { background-color: #f48fb1; }
        .purple { background-color: #ba68c8; }
        .orange { background-color: #ffb74d; }
        .yellow { background-color: #fff176; }
        .green { background-color: #81c784; }
        .teal { background-color: #4db6ac; }
        .grey { background-color: #b0bec5; }

        /* The Tint Trick: Making emojis match the square color */
        /* Note: Filters work slightly differently across devices, but this forces a strong color shift */
        .red span { filter: drop-shadow(0 0 0 #ff5252) contrast(1.5) sepia(1) hue-rotate(-50deg) saturate(5); }
        .green span { filter: drop-shadow(0 0 0 #81c784) sepia(1) hue-rotate(60deg) saturate(3); }
        .pink span { filter: drop-shadow(0 0 0 #f48fb1) contrast(0.8) brightness(1.2); }

        /* Locked / Shadow Square */
        .chord-btn.locked {
            background-color: rgba(0, 0, 0, 0.05) !important;
            box-shadow: inset 0 2px 5px rgba(0,0,0,0.05);
            cursor: not-allowed;
        }
        .chord-btn.locked span { display: none; }

        #msg { font-weight: 600; color: #636e72; min-height: 1.5rem; text-align: center; }
    </style>
</head>
<body>

<div class="container">
    <h1>PRODIGIES-EGUCHI</h1>

    <div class="stats">
        <div class="stat-group"><span id="streak" class="stat-val">0</span><span class="stat-label">Streak</span></div>
        <div class="stat-group"><span id="strikes" class="stat-val" style="color:#ff5252">0</span><span class="stat-label">Strikes</span></div>
    </div>

    <div class="game-panel">
        <div class="progress-container"><div id="progress-bar" class="progress-bar"></div></div>
        
        <button id="play-btn" onclick="startRound()">Start Level</button>
        <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
        <div id="msg">Practice Mode: Tap animals to learn</div>

        <div class="grid">
            <button class="chord-btn red" id="btn-red" onclick="handleInput('red')"><span>🐱</span></button>
            <button class="chord-btn brown" id="btn-brown" onclick="handleInput('brown')"><span>🐻</span></button>
            <button class="chord-btn pink locked" id="btn-pink" onclick="handleInput('pink')"><span>🐷</span></button>
            <button class="chord-btn purple locked" id="btn-purple" onclick="handleInput('purple')"><span>👾</span></button>
            <button class="chord-btn orange locked" id="btn-orange" onclick="handleInput('orange')"><span>🦊</span></button>
            <button class="chord-btn yellow locked" id="btn-yellow" onclick="handleInput('yellow')"><span>🐥</span></button>
            <button class="chord-btn green locked" id="btn-green" onclick="handleInput('green')"><span>🐸</span></button>
            <button class="chord-btn teal locked" id="btn-teal" onclick="handleInput('teal')"><span>🐢</span></button>
            <button class="chord-btn grey locked" id="btn-grey" onclick="handleInput('grey')"><span>🐘</span></button>
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
    let isGameActive = false;

    async function loadSound(name) {
        try {
            const response = await fetch(`${name}.wav`);
            const arrayBuffer = await response.arrayBuffer();
            soundBuffers[name] = await audioCtx.decodeAudioData(arrayBuffer);
        } catch (e) { console.log("Sound load failed for " + name); }
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

    function replaySound() { if (currentTarget) playSound(currentTarget); }

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
        document.getElementById('msg').innerText = "Which animal is singing?";
    }

    function handleInput(choice) {
        if (document.getElementById(`btn-${choice}`).classList.contains('locked')) return;

        if (!isGameActive) {
            playSound(choice);
            return;
        }

        if (choice === currentTarget) {
            streak++;
            document.getElementById('msg').innerText = "Correct! ✨";
            if (streak >= 10 && activeCount < progression.length) {
                activeCount++;
                streak = 0;
                updateUI();
                isGameActive = false;
                document.getElementById('play-btn').style.display = "block";
                document.getElementById('replay-btn').style.display = "none";
                document.getElementById('msg').innerText = "New animal unlocked! Practice it.";
            } else {
                updateUI();
                setTimeout(nextQuestion, 1000);
            }
        } else {
            strikes++;
            streak = 0;
            document.getElementById('msg').innerText = "Try again!";
            updateUI();
            if (strikes >= 3) {
                alert("Restarting level...");
                strikes = 0;
                activeCount = 2;
                updateUI();
            }
        }
    }

    function updateUI() {
        document.getElementById('streak').innerText = streak;
        document.getElementById('strikes').innerText = strikes;
        document.getElementById('progress-bar').style.width = (streak * 10) + "%";
        progression.forEach((color, i) => {
            const btn = document.getElementById(`btn-${color}`);
            if (i < activeCount) btn.classList.remove('locked');
            else btn.classList.add('locked');
        });
    }
</script>

</body>
</html>
