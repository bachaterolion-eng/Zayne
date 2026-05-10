<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Pitch Training</title>
    <style>
        * { box-sizing: border-box; } /* Prevents padding from shifting elements */

        :root {
            --bg-dark: #000000;
            --panel-bg: #f0f2f5;
            --text-main: #ffffff;
            --text-sub: #a0a0a0;
            --progress-bg: #dfe6e9;
            --progress-fill: #2ecc71;
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
            overflow-x: hidden;
        }

        .container {
            width: 100%;
            max-width: 500px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        /* Centered Title - Single Instance */
        h1 {
            color: #1a73e8;
            font-size: 1.8rem;
            font-weight: 700;
            margin: 20px 0;
            padding: 0;
            width: 100%;
            text-align: center;
            display: block;
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
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }

        .progress-container {
            width: 100%;
            max-width: 250px;
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

        #play-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 45px; 
            border-radius: 40px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.1s;
        }

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
            transition: transform 0.1s, background-color 0.3s;
            border: 2px solid transparent;
        }

        .chord-btn.locked {
            cursor: not-allowed;
            background-color: transparent !important;
            border: 2px dotted;
            opacity: 0.5;
        }

        /* Silhouette Border Colors */
        .chord-btn.locked.red    { border-color: #ff5252; }
        .chord-btn.locked.brown  { border-color: #8d6e63; }
        .chord-btn.locked.pink   { border-color: #f48fb1; }
        .chord-btn.locked.purple { border-color: #ba68c8; }
        .chord-btn.locked.orange { border-color: #ffb74d; }
        .chord-btn.locked.yellow { border-color: #fff176; }
        .chord-btn.locked.green  { border-color: #81c784; }
        .chord-btn.locked.teal   { border-color: #4db6ac; }
        .chord-btn.locked.grey   { border-color: #b0bec5; }

        /* Unlocked Background Colors */
        .chord-btn:not(.locked).red    { background-color: #ff5252; }
        .chord-btn:not(.locked).brown  { background-color: #8d6e63; }
        .chord-btn:not(.locked).pink   { background-color: #f48fb1; }
        .chord-btn:not(.locked).purple { background-color: #ba68c8; }
        .chord-btn:not(.locked).orange { background-color: #ffb74d; }
        .chord-btn:not(.locked).yellow { background-color: #fff176; }
        .chord-btn:not(.locked).green  { background-color: #81c784; }
        .chord-btn:not(.locked).teal   { background-color: #4db6ac; }
        .chord-btn:not(.locked).grey   { background-color: #b0bec5; }

        #msg { margin-top: 15px; font-size: 1rem; font-weight: 600; color: #636e72; min-height: 1.2rem; }
        #timer-display { font-weight: 700; color: #e74c3c; margin-top: 5px; font-size: 1.1rem; min-height: 1.2rem;}

        @media (max-width: 420px) {
            .chord-btn { width: 85px; height: 85px; }
            .stat-val { font-size: 4.5rem; }
        }
    </style>
</head>
<body>

    <div class="container">

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

            <button id="play-btn" onclick="handlePlayButton()">Listen</button>
            <div id="msg">Ready</div>
            <div id="timer-display"></div>

            <div class="grid">
                <button class="chord-btn red" id="btn-red" onclick="checkAnswer('red')"></button>
                <button class="chord-btn brown locked" id="btn-brown" onclick="checkAnswer('brown')"></button>
                <button class="chord-btn pink locked" id="btn-pink" onclick="checkAnswer('pink')"></button>
                <button class="chord-btn purple locked" id="btn-purple" onclick="checkAnswer('purple')"></button>
                <button class="chord-btn orange locked" id="btn-orange" onclick="checkAnswer('orange')"></button>
                <button class="chord-btn yellow locked" id="btn-yellow" onclick="checkAnswer('yellow')"></button>
                <button class="chord-btn green locked" id="btn-green" onclick="checkAnswer('green')"></button>
                <button class="chord-btn teal locked" id="btn-teal" onclick="checkAnswer('teal')"></button>
                <button class="chord-btn grey locked" id="btn-grey" onclick="checkAnswer('grey')"></button>
            </div>
        </div>
    </div>

    <script>
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        const soundBuffers = {};
        const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey'];
        
        let activeCount = 1; 
        let streak = 0;
        let strikes = 0;
        let currentTarget = null;
        const streakGoal = 10;
        
        let timeLeft = 10;
        let timerInterval = null;
        let isTimerRunning = false;

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

        function startTimer() {
            if (isTimerRunning) return; 
            isTimerRunning = true;
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
            isTimerRunning = false;
            document.getElementById('timer-display').innerText = "";
        }

        function updateTimerUI() {
            document.getElementById('timer-display').innerText = timeLeft > 0 ? `Time: ${timeLeft}s` : "";
        }

        function handleMistake(message) {
            strikes++;
            streak = 0;
            currentTarget = null;
            if (strikes >= 3) {
                alert("3 Strikes! Returning to the beginning.");
                strikes = 0;
                activeCount = 1;
            } else {
                document.getElementById('msg').innerText = message;
                document.getElementById('msg').style.color = "#e74c3c";
            }
            updateUI();
            setTimeout(handlePlayButton, 1500);
        }

        function handlePlayButton() {
            if (!currentTarget) {
                const available = progression.slice(0, activeCount);
                currentTarget = available[Math.floor(Math.random() * available.length)];
                startTimer(); 
            }
            playSound(currentTarget);
            document.getElementById('msg').innerText = "Listen closely...";
            document.getElementById('msg').style.color = "#636e72";
        }

        function checkAnswer(choice) {
            if (!currentTarget) return;
            const btn = document.getElementById(`btn-${choice}`);
            if (btn.classList.contains('locked')) return;
            if (choice === currentTarget) {
                stopTimer();
                streak++;
                document.getElementById('msg').innerText = "Correct! ✨";
                document.getElementById('msg').style.color = "#2ecc71";
                if (streak >= streakGoal && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                    alert("Next level unlocked!");
                }
                currentTarget = null;
                updateUI();
                setTimeout(handlePlayButton, 1000);
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
                if (btn && index < activeCount) {
                    btn.classList.remove('locked');
                } else {
                    btn.classList.add('locked');
                }
            });
        }
    </script>
</body>
</html>

```
