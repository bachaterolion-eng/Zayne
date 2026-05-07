<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Pitch Training</title>
    <style>
        :root {
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --text-main: #2d3436;
            --text-sub: #636e72;
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
            max-width: 500px;
            padding: 40px 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            text-align: center;
        }

        h1 {
            font-weight: 300;
            font-size: 1.8rem;
            margin-bottom: 10px;
            letter-spacing: 1px;
        }

        .stats {
            margin-bottom: 30px;
        }

        .stat-val {
            display: block;
            font-size: 3.5rem;
            font-weight: 700;
            color: #000000;
            line-height: 1;
        }

        .stat-label {
            font-size: 0.8rem;
            text-transform: uppercase;
            letter-spacing: 2px;
            color: var(--text-sub);
        }

        .play-area {
            margin-bottom: 40px;
        }

        #play-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 18px 45px;
            border-radius: 50px;
            font-size: 1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0 10px 20px rgba(0,0,0,0.1);
        }

        #play-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 15px 25px rgba(0,0,0,0.15);
        }

        #play-btn:active {
            transform: translateY(0);
        }

        /* 3-Column Grid */
        .grid {
            display: grid;
            grid-template-columns: repeat(3, 1fr);
            gap: 15px;
            width: 100%;
        }

        .chord-btn {
            aspect-ratio: 1 / 1;
            border: none;
            border-radius: 12px;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
        }

        .chord-btn:hover:not(.locked) {
            transform: scale(1.05);
            filter: brightness(1.1);
        }

        .chord-btn:active:not(.locked) {
            transform: scale(0.95);
        }

        .chord-btn.locked {
            background-color: #dfe6e9 !important;
            opacity: 0.3;
            cursor: not-allowed;
            box-shadow: none;
        }

        /* Colors */
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
            margin-top: 25px;
            font-size: 1rem;
            min-height: 1.5rem;
            font-weight: 500;
        }

        @media (max-width: 400px) {
            .grid { gap: 10px; }
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Pitch Training</h1>
        
        <div class="stats">
            <span id="streak" class="stat-val">0</span>
            <span class="stat-label">Consecutive</span>
        </div>

        <div class="play-area">
            <button id="play-btn" onclick="handlePlayButton()">Listen</button>
            <div id="msg"></div>
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
        const chords = {
            'red':    [261.63, 329.63, 392.00],
            'brown':  [261.63, 349.23, 440.00],
            'pink':   [246.94, 293.66, 392.00],
            'purple': [220.00, 261.63, 349.23],
            'orange': [293.66, 392.00, 493.88],
            'yellow': [329.63, 392.00, 523.25],
            'green':  [349.23, 440.00, 523.25],
            'teal':   [392.00, 493.88, 587.33],
            'grey':   [392.00, 523.25, 659.25]
        };

        const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey'];
        
        let activeCount = 2;
        let streak = 0;
        let currentTarget = null;
        let audioCtx = null;

        function updateUI() {
            document.getElementById('streak').innerText = streak;
            progression.forEach((color, index) => {
                const btn = document.getElementById(`btn-${color}`);
                if (btn && index < activeCount) btn.classList.remove('locked');
            });
        }

        function playSound(frequencies) {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            if (audioCtx.state === 'suspended') audioCtx.resume();

            const now = audioCtx.currentTime;
            frequencies.forEach(freq => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(freq, now);
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.1, now + 0.02);
                gain.gain.exponentialRampToValueAtTime(0.0001, now + 1.8);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start(now);
                osc.stop(now + 2.0);
            });
        }

        function handlePlayButton() {
            if (!currentTarget) {
                const available = progression.slice(0, activeCount);
                currentTarget = available[Math.floor(Math.random() * available.length)];
            }
            playSound(chords[currentTarget]);
            document.getElementById('msg').innerText = "Focus...";
            document.getElementById('msg').style.color = "#636e72";
        }

        function checkAnswer(choice) {
            if (!currentTarget) return;

            if (choice === currentTarget) {
                streak++;
                document.getElementById('msg').innerText = "Correct";
                document.getElementById('msg').style.color = "#2ecc71";
                
                if (streak >= 20 && activeCount < progression.length) {
                    activeCount++;
                    streak = 0; 
                    alert("Next level unlocked.");
                }
                
                currentTarget = null; 
                updateUI();
                setTimeout(handlePlayButton, 1000);
            } else {
                streak = 0;
                document.getElementById('msg').innerText = "Reset";
                document.getElementById('msg').style.color = "#e74c3c";
                updateUI();
                playSound(chords[currentTarget]); 
            }
        }

        updateUI();
    </script>
</body>
</html>
