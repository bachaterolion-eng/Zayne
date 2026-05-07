<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Progressive Pitch Training</title>
    <style>
        body {
            font-family: -apple-system, system-ui, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: #f8f9fa;
            margin: 0;
            padding: 20px;
            color: #333;
        }

        .header { text-align: center; margin-bottom: 20px; }
        
        .stats { 
            background: #fff; 
            padding: 15px; 
            border-radius: 12px; 
            box-shadow: 0 2px 10px rgba(0,0,0,0.05); 
            margin-bottom: 20px; 
            width: 100%; 
            max-width: 300px; 
            text-align: center;
        }
        
        .stat-label { font-size: 0.9rem; color: #666; text-transform: uppercase; letter-spacing: 1px; }
        .stat-val { display: block; font-size: 2.5rem; font-weight: bold; color: #e53935; }

        .play-area { margin-bottom: 30px; text-align: center; }
        #play-btn { 
            background: #333; 
            color: white; 
            border: none; 
            padding: 20px 40px; 
            border-radius: 50px; 
            font-size: 1.2rem; 
            cursor: pointer; 
            box-shadow: 0 4px 15px rgba(0,0,0,0.2); 
            transition: background 0.2s;
        }
        #play-btn:active { transform: scale(0.98); }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
            gap: 12px;
            width: 100%;
            max-width: 600px;
        }

        .chord-btn {
            border: none;
            height: 90px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            border-radius: 12px;
            transition: all 0.2s;
            opacity: 1;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-size: 1rem;
            box-shadow: 0 4px 0px rgba(0,0,0,0.1);
        }

        .chord-btn.locked { opacity: 0.05; cursor: not-allowed; pointer-events: none; }
        .chord-btn:active:not(.locked) { transform: translateY(3px); box-shadow: none; }

        /* Custom Colors */
        .red { background-color: #e53935; }
        .brown { background-color: #795548; }
        .pink { background-color: #f06292; }
        .purple { background-color: #9c27b0; }
        .orange { background-color: #fb8c00; }
        .yellow { background-color: #ffeb3b; color: #424242; }
        .green { background-color: #43a047; }
        .teal { background-color: #008080; }
        .grey { background-color: #9e9e9e; }

        #msg { margin-top: 20px; font-weight: bold; height: 24px; font-size: 1.1rem; }
    </style>
</head>
<body>

    <div class="header">
        <h1>Eguchi Pitch Training</h1>
        <p id="level-text">Level 1: 2 Chords Active</p>
    </div>

    <div class="stats">
        <span class="stat-label">Consecutive Correct</span>
        <span id="streak" class="stat-val">0</span>
        <small style="color: #999;">Get 20 to unlock next chord</small>
    </div>

    <div class="play-area">
        <button id="play-btn" onclick="playRandomChord()">Listen to Chord</button>
        <div id="msg"></div>
    </div>

    <div class="grid" id="chord-grid">
        <button class="chord-btn red" onclick="checkAnswer('red')">RED</button>
        <button class="chord-btn brown" onclick="checkAnswer('brown')">BROWN</button>
        <button class="chord-btn pink locked" id="btn-pink" onclick="checkAnswer('pink')">PINK</button>
        <button class="chord-btn purple locked" id="btn-purple" onclick="checkAnswer('purple')">PURPLE</button>
        <button class="chord-btn orange locked" id="btn-orange" onclick="checkAnswer('orange')">ORANGE</button>
        <button class="chord-btn yellow locked" id="btn-yellow" onclick="checkAnswer('yellow')">YELLOW</button>
        <button class="chord-btn green locked" id="btn-green" onclick="checkAnswer('green')">GREEN</button>
        <button class="chord-btn teal locked" id="btn-teal" onclick="checkAnswer('teal')">TEAL</button>
        <button class="chord-btn grey locked" id="btn-grey" onclick="checkAnswer('grey')">GREY</button>
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
            document.getElementById('level-text').innerText = `Level ${activeCount - 1}: ${activeCount} Chords Active`;
            
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

        function playRandomChord() {
            const available = progression.slice(0, activeCount);
            currentTarget = available[Math.floor(Math.random() * available.length)];
            playSound(chords[currentTarget]);
            document.getElementById('msg').innerText = "Listen...";
            document.getElementById('msg').style.color = "#333";
        }

        function checkAnswer(choice) {
            if (!currentTarget) return;

            if (choice === currentTarget) {
                streak++;
                document.getElementById('msg').innerText = "Correct! ✨";
                document.getElementById('msg').style.color = "green";
                
                if (streak >= 20 && activeCount < progression.length) {
                    activeCount++;
                    streak = 0; 
                    alert("Well done! A new chord has been unlocked.");
                }
                
                currentTarget = null;
                updateUI();
                setTimeout(playRandomChord, 1200);
            } else {
                // Mistake: Reset streak to zero
                streak = 0;
                document.getElementById('msg').innerText = "Incorrect. Streak reset! ❌";
                document.getElementById('msg').style.color = "red";
                updateUI();
                // Play correct sound so they can hear the difference
                playSound(chords[currentTarget]); 
            }
        }

        updateUI();
    </script>
</body>
</html>
