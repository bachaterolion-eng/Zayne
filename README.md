<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Piano Pitch Training</title>
    <style>
        :root {
            --bg-color: #f0f2f5;
            --text-main: #2d3436;
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
            max-width: 650px;
            padding: 10px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        .stats { margin-bottom: 10px; }
        .stat-val { display: block; font-size: 4.5rem; font-weight: 700; color: #000000; line-height: 1; }
        .stat-label { font-size: 0.9rem; text-transform: uppercase; letter-spacing: 2px; color: var(--text-sub); }

        /* Progress Bar Container */
        .progress-container {
            width: 100%;
            max-width: 500px;
            height: 14px;
            background-color: var(--progress-bg);
            border-radius: 10px;
            margin: 20px 0 30px 0;
            overflow: hidden;
            box-shadow: inset 0 1px 3px rgba(0,0,0,0.1);
        }

        /* The moving part of the Progress Bar */
        .progress-bar {
            height: 100%;
            width: 0%;
            background-color: var(--progress-fill);
            transition: width 0.4s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .play-area { margin-bottom: 40px; }
        #play-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 22px 70px;
            border-radius: 50px;
            font-size: 1.2rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
        }

        #play-btn:hover { transform: translateY(-2px); box-shadow: 0 15px 25px rgba(0,0,0,0.15); }
        #play-btn:active { transform: translateY(0); }

        .grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 25px; 
            width: 100%;
        }

        .chord-btn {
            aspect-ratio: 1 / 1;
            border: none;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 8px 16px rgba(0,0,0,0.08);
        }

        .chord-btn:hover:not(.locked) {
            transform: scale(1.03);
            filter: brightness(1.05);
        }

        .chord-btn:active:not(.locked) {
            transform: scale(0.97);
        }

        .chord-btn.locked {
            background-color: #dfe6e9 !important;
            opacity: 0.15;
            cursor: not-allowed;
            box-shadow: none;
        }

        /* Color definitions as per Gemini_Generated_Image_mzqbjcmzqbjcmzqb.png */
        .red { background-color: #ff5252; }
        .brown { background-color: #8d6e63; }
        .pink { background-color: #f48fb1; }
        .purple { background-color: #ba68c8; }
        .orange { background-color: #ffb74d; }
        .yellow { background-color: #fff176; }
        .green { background-color: #81c784; }
        .teal { background-color: #4db6ac; }
        .grey { background-color: #b0bec5; }

        #msg { margin-top: 25px; font-size: 1.1rem; font-weight: 500; height: 1.5rem; letter-spacing: 0.5px; }
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
            <div id="msg">Loading piano samples...</div>
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

        // Load the .wav files from the root directory
        async function loadSound(name) {
            try {
                const response = await fetch(`${name}.wav`); 
                const arrayBuffer = await response.arrayBuffer();
                soundBuffers[name] = await audioCtx.decodeAudioData(arrayBuffer);
            } catch (e) {
                console.error(`Error: Could not find ${name}.wav in the folder.`, e);
            }
        }

        // Initialize audio on start
        Promise.all(progression.map(name => loadSound(name))).then(() => {
            document.getElementById('msg').innerText = "Ready";
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
            document.getElementById('msg').innerText = "Listen closely...";
            document.getElementById('msg').style.color = "#636e72";
        }

        function checkAnswer(choice) {
            if (!currentTarget) return;

            if (choice === currentTarget) {
                streak++;
                document.getElementById('msg').innerText = "Correct! ✨";
                document.getElementById('msg').style.color = "#2ecc71";
                
                if (streak >= 20 && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                    alert("Well done! Next level unlocked.");
                }
                
                currentTarget = null;
                updateUI();
                setTimeout(handlePlayButton, 1000);
            } else {
                streak = 0;
                document.getElementById('msg').innerText = "Incorrect. Try again!";
                document.getElementById('msg').style.color = "#e74c3c";
                updateUI();
                playSound(currentTarget); // Replay the correct sound for reference
            }
        }

        function updateUI() {
            // Update the Black streak number
            document.getElementById('streak').innerText = streak;

            // Fill the progress bar (calculated toward 20)
            const progressPercent = (streak / 20) * 100;
            document.getElementById('progress-bar').style.width = `${progressPercent}%`;

            // Update button visibility based on level
            progression.forEach((color, index) => {
                const btn = document.getElementById(`btn-${color}`);
                if (btn && index < activeCount) btn.classList.remove('locked');
            });
        }
    </script>
</body>
</html>
