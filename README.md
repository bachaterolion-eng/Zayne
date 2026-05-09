<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PRODIGIES - EGUCHI</title>
    <style>
        :root {
            --bg-color: #ffffff;
            --text-main: #000000;
            --text-sub: #888888;
            --progress-bg: #eeeeee;
            --progress-fill: #000000;
        }

        body {
            font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
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
            max-width: 800px; /* Wider container for bigger squares */
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        /* Fancy Bold Black Logo */
        h1 {
            font-size: 2.5rem;
            font-weight: 900;
            letter-spacing: 8px;
            text-transform: uppercase;
            margin: 0 0 40px 0;
            color: #000000;
            border-bottom: 4px solid #000000;
            padding-bottom: 10px;
            display: inline-block;
        }

        .stats { margin-bottom: 10px; }
        .stat-val { display: block; font-size: 5rem; font-weight: 800; color: #000000; line-height: 1; }
        .stat-label { font-size: 0.8rem; text-transform: uppercase; letter-spacing: 3px; color: var(--text-sub); }

        .progress-container {
            width: 100%;
            max-width: 500px;
            height: 8px;
            background-color: var(--progress-bg);
            border-radius: 0; /* Modern flat look */
            margin: 25px 0 40px 0;
            overflow: hidden;
        }

        .progress-bar {
            height: 100%;
            width: 0%;
            background-color: var(--progress-fill);
            transition: width 0.5s cubic-bezier(0.19, 1, 0.22, 1);
        }

        .play-area { margin-bottom: 50px; }
        #play-btn {
            background: #000000;
            color: white;
            border: none;
            padding: 24px 80px;
            border-radius: 0; /* Modern square edges */
            font-size: 1.1rem;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 2px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        #play-btn:hover { background: #333333; transform: scale(1.02); }

        /* Massive Squares Grid */
        .grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 30px; 
            width: 100%;
        }

        .chord-btn {
            aspect-ratio: 1 / 1;
            border: none;
            border-radius: 4px; /* Clean sharp edges with slight rounding */
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.25, 0.46, 0.45, 0.94);
            box-shadow: 0 10px 30px rgba(0,0,0,0.05);
        }

        .chord-btn:hover:not(.locked) {
            transform: translateY(-5px);
            box-shadow: 0 15px 40px rgba(0,0,0,0.15);
        }

        .chord-btn.locked {
            background-color: #f5f5f5 !important;
            opacity: 0.1;
            cursor: not-allowed;
            box-shadow: none;
        }

        /* Color palette from Gemini_Generated_Image_mzqbjcmzqbjcmzqb.png */
        .red { background-color: #ff5252; }
        .brown { background-color: #8d6e63; }
        .pink { background-color: #f48fb1; }
        .purple { background-color: #ba68c8; }
        .orange { background-color: #ffb74d; }
        .yellow { background-color: #fff176; }
        .green { background-color: #81c784; }
        .teal { background-color: #4db6ac; }
        .grey { background-color: #b0bec5; }

        #msg { 
            margin-top: 30px; 
            font-size: 0.9rem; 
            text-transform: uppercase; 
            letter-spacing: 2px; 
            font-weight: bold;
            height: 1.5rem; 
        }

        @media (max-width: 600px) {
            h1 { font-size: 1.8rem; }
            .grid { gap: 15px; }
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Prodigies - Eguchi</h1>

        <div class="stats">
            <span id="streak" class="stat-val">0</span>
            <span class="stat-label">Consecutive</span>
        </div>

        <div class="progress-container">
            <div id="progress-bar" class="progress-bar"></div>
        </div>

        <div class="play-area">
            <button id="play-btn" onclick="handlePlayButton()">Listen</button>
            <div id="msg">Initializing...</div>
        </div>

        <div class="grid">
            <button class="chord-btn red" onclick="checkAnswer('red')"></button>
            <button class="chord-btn brown" onclick="checkAnswer('brown')"></button>
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
        
        let activeCount = 2;
        let streak = 0;
        let currentTarget = null;
        const streakGoal = 10;

        async function loadSound(name) {
            try {
                const response = await fetch(`${name}.wav`); 
                const arrayBuffer = await response.arrayBuffer();
                soundBuffers[name] = await audioCtx.decodeAudioData(arrayBuffer);
            } catch (e) {
                console.error(`Error: Could not find ${name}.wav`, e);
            }
        }

        Promise.all(progression.map(name => loadSound(name))).then(() => {
            document.getElementById('msg').innerText = "System Ready";
        });

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
            document.getElementById('msg').innerText = "Focus on Pitch";
            document.getElementById('msg').style.color = "#888";
        }

        function checkAnswer(choice) {
            if (!currentTarget) return;

            if (choice === currentTarget) {
                streak++;
                document.getElementById('msg').innerText = "Correct";
                document.getElementById('msg').style.color = "#2ecc71";
                
                if (streak >= streakGoal && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                }
                
                currentTarget = null;
                updateUI();
                setTimeout(handlePlayButton, 800);
            } else {
                streak = 0;
                document.getElementById('msg').innerText = "Reset";
                document.getElementById('msg').style.color = "#ff5252";
                updateUI();
                playSound(currentTarget); 
            }
        }

        function updateUI() {
            document.getElementById('streak').innerText = streak;
            const progressPercent = (streak / streakGoal) * 100;
            document.getElementById('progress-bar').style.width = `${progressPercent}%`;

            progression.forEach((color, index) => {
                const btn = document.getElementById(`btn-${color}`);
                if (btn && index < activeCount) btn.classList.remove('locked');
            });
        }
    </script>
</body>
</html>

```
