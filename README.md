<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Pitch Training</title>
    <style>
        :root {
            --bg-color: #f0f2f5;
            --text-main: #000000;
            --text-sub: #636e72;
            --progress-bg: #dfe6e9;
            --progress-fill: #2ecc71;
        }

        body {
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            width: 100%;
            max-width: 600px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        h1 {
            color: #000000 !important;
            font-size: 2rem;
            font-weight: 700;
            margin: 0 0 20px 0;
            text-align: center;
            width: 100%;
        }

        .stats { margin-bottom: 5px; }
        .stat-val { display: block; font-size: 5.5rem; font-weight: 700; color: #000000; line-height: 1; }
        .stat-label { font-size: 0.85rem; text-transform: uppercase; letter-spacing: 2px; color: var(--text-sub); }

        .progress-container {
            width: 80%;
            max-width: 300px;
            height: 12px;
            background-color: var(--progress-bg);
            border-radius: 10px;
            margin: 15px 0 25px 0;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            width: 0%;
            background-color: var(--progress-fill);
            transition: width 0.4s ease;
        }

        .play-area { margin-bottom: 30px; }
        
        #play-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 45px; 
            border-radius: 40px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: transform 0.1s;
        }

        #play-btn:active { transform: scale(0.96); }

        .grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 20px; 
            justify-items: center;
            width: auto;
        }

        .chord-btn {
            width: 120px;
            height: 120px;
            border-radius: 20px; 
            cursor: pointer;
            transition: transform 0.1s, background-color 0.3s, border 0.3s;
            box-shadow: 0 6px 12px rgba(0,0,0,0.08);
            border: 2px solid transparent;
        }

        /* Silhouette Logic: Double the thickness (2px) and dotted */
        .chord-btn.locked {
            cursor: not-allowed;
            box-shadow: none;
            background-color: transparent !important;
            border-width: 2px; /* Changed from 1px to 2px */
            border-style: dotted;
            opacity: 0.8;
        }

        .chord-btn.locked:active { transform: none; }

        /* Silhouette Colors (Locked) */
        .chord-btn.locked.red    { border-color: #ff5252; }
        .chord-btn.locked.brown  { border-color: #8d6e63; }
        .chord-btn.locked.pink   { border-color: #f48fb1; }
        .chord-btn.locked.purple { border-color: #ba68c8; }
        .chord-btn.locked.orange { border-color: #ffb74d; }
        .chord-btn.locked.yellow { border-color: #fff176; }
        .chord-btn.locked.green  { border-color: #81c784; }
        .chord-btn.locked.teal   { border-color: #4db6ac; }
        .chord-btn.locked.grey   { border-color: #b0bec5; }

        /* Unlocked Styles (Solid Full Color) */
        .chord-btn:not(.locked) { border: 2px solid transparent; }
        .chord-btn:not(.locked).red    { background-color: #ff5252; }
        .chord-btn:not(.locked).brown  { background-color: #8d6e63; }
        .chord-btn:not(.locked).pink   { background-color: #f48fb1; }
        .chord-btn:not(.locked).purple { background-color: #ba68c8; }
        .chord-btn:not(.locked).orange { background-color: #ffb74d; }
        .chord-btn:not(.locked).yellow { background-color: #fff176; }
        .chord-btn:not(.locked).green  { background-color: #81c784; }
        .chord-btn:not(.locked).teal   { background-color: #4db6ac; }
        .chord-btn:not(.locked).grey   { background-color: #b0bec5; }

        @media (max-width: 420px) {
            .chord-btn {
                width: 100px;
                height: 100px;
            }
        }

        .chord-btn:active:not(.locked) { transform: scale(0.92); }

        #msg { margin-top: 20px; font-size: 1.1rem; min-height: 1.5rem; font-weight: 500; color: var(--text-sub); }
    </style>
</head>
<body>

    <div class="container">
        <div class="stats">
            <span id="streak" class="stat-val">0</span>
            <span class="stat-label">Consecutive Correct</span>
        </div>

        <div class="progress-container">
            <div id="progress-bar" class="progress-bar"></div>
        </div>

        <div class="play-area">
            <button id="play-btn" onclick="handlePlayButton()">Listen</button>
            <div id="msg">Ready</div>
        </div>

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

    <script>
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        const soundBuffers = {};
        const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey'];
        
        let activeCount = 1; 
        let streak = 0;
        let currentTarget = null;
        const streakGoal = 10;

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

        function handlePlayButton() {
            if (!currentTarget) {
                const available = progression.slice(0, activeCount);
                currentTarget = available[Math.floor(Math.random() * available.length)];
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
                streak = 0;
                document.getElementById('msg').innerText = "Try again!";
                document.getElementById('msg').style.color = "#e74c3c";
                updateUI();
            }
        }

        function updateUI() {
            document.getElementById('streak').innerText = streak;
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
