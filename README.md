
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
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
            background-color: var(--bg-dark);
            color: var(--text-main);
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: flex-start;
            min-height: 100vh;
        }

        .container {
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }

        h1 {
            color: #1a73e8;
            font-size: 1.8rem;
            font-weight: 700;
            margin: 20px 0;
            text-align: center;
            width: 100%;
        }

        .stats { 
            margin-bottom: 20px; 
            display: flex; 
            justify-content: center; 
            gap: 20px; 
        }
        
        .stat-group { display: flex; flex-direction: column; align-items: center; width: 120px; }
        .stat-val { font-size: 5.5rem; font-weight: 700; color: #ffffff; line-height: 1; }
        .stat-label { font-size: 0.85rem; text-transform: uppercase; color: var(--text-sub); }

        .game-panel {
            background-color: var(--panel-bg);
            padding: 30px;
            border-radius: 40px;
            display: flex;
            flex-direction: column;
            align-items: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
        }

        .progress-container {
            width: 100%;
            height: 10px;
            background-color: var(--progress-bg);
            border-radius: 10px;
            margin-bottom: 25px;
            overflow: hidden;
        }

        .progress-bar { height: 100%; width: 0%; background-color: var(--progress-fill); transition: width 0.4s; }

        #play-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 45px; 
            border-radius: 40px;
            font-size: 1.1rem;
            cursor: pointer;
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
            border-radius: 24px; 
            cursor: pointer;
            border: none;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: transform 0.1s;
        }

        .chord-btn svg {
            width: 65%;
            height: 65%;
            fill: white; /* This makes the animal white on the colored background */
        }

        /* Locked / Shadow Square State */
        .chord-btn.locked {
            background-color: rgba(0, 0, 0, 0.05) !important;
            cursor: not-allowed;
            box-shadow: inset 0 2px 8px rgba(0,0,0,0.05);
        }

        .chord-btn.locked svg {
            display: none; /* Hide the animal until unlocked */
        }

        /* Level Colors */
        .red { background-color: #ff5252; }
        .brown { background-color: #8d6e63; }
        .pink { background-color: #f48fb1; }
        .purple { background-color: #ba68c8; }
        .orange { background-color: #ffb74d; }
        .yellow { background-color: #fff176; }
        .green { background-color: #81c784; }
        .teal { background-color: #4db6ac; }
        .grey { background-color: #b0bec5; }

        #msg { margin-top: 15px; font-weight: 600; color: #636e72; text-align: center; }
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
            <div id="msg">Practice Mode: Tap animals to learn</div>

            <div class="grid">
                <button class="chord-btn red" id="btn-red" onclick="handleInput('red')">
                    <svg viewBox="0 0 24 24"><path d="M12,8L10.67,8.09C9.81,7.07 7.4,4.5 5,4.5C5,4.5 3.03,7.46 4.96,11.41C4.41,12.24 4.07,12.67 4,13.66C4,13.66 2,13.66 2,15.66C2,17.66 4,17.66 4,17.66C4,17.66 4,20.66 6,20.66C8,20.66 12,20.66 12,20.66C12,20.66 16,20.66 18,20.66C20,20.66 20,17.66 20,17.66C20,17.66 22,17.66 22,15.66C22,13.66 20,13.66 20,13.66C19.93,12.67 19.59,12.24 19.04,11.41C20.97,7.46 19,4.5 19,4.5C16.6,4.5 14.19,7.07 13.33,8.09L12,8Z"/></svg>
                </button>
                <button class="chord-btn brown" id="btn-brown" onclick="handleInput('brown')">
                    <svg viewBox="0 0 24 24"><path d="M18.5,2C17.12,2 16,3.12 16,4.5C16,4.91 16.1,5.3 16.27,5.64C15.04,5.23 13.61,5 12,5C10.39,5 8.96,5.23 7.73,5.64C7.9,5.3 8,4.91 8,4.5C8,3.12 6.88,2 5.5,2C4.12,2 3,3.12 3,4.5C3,5.74 3.9,6.77 5.09,6.97C5.03,7.31 5,7.65 5,8C5,13 9,15 12,15C15,15 19,13 19,8C19,7.65 18.97,7.31 18.91,6.97C20.1,6.77 21,5.74 21,4.5C21,3.12 19.88,2 18.5,2Z"/></svg>
                </button>
                <button class="chord-btn pink locked" id="btn-pink" onclick="handleInput('pink')">
                    <svg viewBox="0 0 24 24"><path d="M12,2C6.48,2 2,6.48 2,12C2,17.52 6.48,22 12,22C17.52,22 22,17.52 22,12C22,6.48 17.52,2 12,2M12,19C9.24,19 7,16.76 7,14C7,11.24 9.24,9 12,9C14.76,9 17,11.24 17,14C17,16.76 14.76,19 12,19M10,13A1,1 0 0,1 11,14A1,1 0 0,1 10,15A1,1 0 0,1 9,14A1,1 0 0,1 10,13M14,13A1,1 0 0,1 15,14A1,1 0 0,1 14,15A1,1 0 0,1 13,14A1,1 0 0,1 14,13Z"/></svg>
                </button>
                <button class="chord-btn purple locked" id="btn-purple" onclick="handleInput('purple')">
                    <svg viewBox="0 0 24 24"><path d="M12,2C6.5,2 2,6.5 2,12C2,17.5 6.5,22 12,22C17.5,22 22,17.5 22,12C22,6.5 17.5,2 12,2M7.5,10.5C8.3,10.5 9,11.2 9,12C9,12.8 8.3,13.5 7.5,13.5C6.7,13.5 6,12.8 6,12C6,11.2 6.7,10.5 7.5,10.5M12,18C10,18 8.3,16.7 7.4,15H16.6C15.7,16.7 14,18 12,18M16.5,13.5C15.7,13.5 15,12.8 15,12C15,11.2 15.7,10.5 16.5,10.5C17.3,10.5 18,11.2 18,12C18,12.8 17.3,13.5 16.5,13.5Z"/></svg>
                </button>
                <button class="chord-btn orange locked" id="btn-orange" onclick="handleInput('orange')"></button>
                <button class="chord-btn yellow locked" id="btn-yellow" onclick="handleInput('yellow')"></button>
                <button class="chord-btn green locked" id="btn-green" onclick="handleInput('green')"></button>
                <button class="chord-btn teal locked" id="btn-teal" onclick="handleInput('teal')"></button>
                <button class="chord-btn grey locked" id="btn-grey" onclick="handleInput('grey')"></button>
            </div>
        </div>
    </div>

    <script>
        const progression = ['red', 'brown', 'pink', 'purple', 'orange', 'yellow', 'green', 'teal', 'grey'];
        let activeCount = 2; // Starts on Red & Brown
        let streak = 0;
        let strikes = 0;
        let currentTarget = null;
        let isGameActive = false;

        function startRound() {
            isGameActive = true;
            document.getElementById('play-btn').style.display = "none";
            nextQuestion();
        }

        function nextQuestion() {
            const available = progression.slice(0, activeCount);
            currentTarget = available[Math.floor(Math.random() * available.length)];
            // playSound(currentTarget); // Logic placeholder
            document.getElementById('msg').innerText = "Listen closely...";
        }

        function handleInput(choice) {
            const btn = document.getElementById(`btn-${choice}`);
            if (btn.classList.contains('locked')) return;

            if (!isGameActive) {
                // playSound(choice); // Practice tap
                return;
            }

            if (choice === currentTarget) {
                streak++;
                document.getElementById('msg').innerText = "Correct!";
                if (streak >= 10 && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                    updateUI();
                    isGameActive = false;
                    document.getElementById('play-btn').style.display = "block";
                } else {
                    updateUI();
                    setTimeout(nextQuestion, 800);
                }
            } else {
                strikes++;
                streak = 0;
                updateUI();
                if (strikes >= 3) {
                    alert("Game Over! Restarting.");
                    location.reload();
                }
            }
        }

        function updateUI() {
            document.getElementById('streak').innerText = streak;
            document.getElementById('strikes').innerText = strikes;
            document.getElementById('progress-bar').style.width = (streak * 10) + "%";
            progression.forEach((color, i) => {
                const b = document.getElementById(`btn-${color}`);
                if (i < activeCount) b.classList.remove('locked');
                else b.classList.add('locked');
            });
        }
    </script>
</body>
</html>

```
