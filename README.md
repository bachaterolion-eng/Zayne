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
            padding: 0;
            text-align: center; /* Centered Header */
            width: 100%;
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
            display: inline-flex;
            flex-direction: column;
            align-items: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            width: auto;
            max-width: fit-content;
        }

        .progress-container {
            width: 100%;
            min-width: 250px;
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

        #play-btn, #replay-btn {
            background: #2d3436;
            color: white;
            border: none;
            padding: 12px 45px; 
            border-radius: 40px;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.2s;
        }

        #replay-btn {
            background: #1a73e8;
            padding: 8px 25px;
            font-size: 0.9rem;
            margin-top: 10px;
            display: none; 
        }

        #replay-btn:hover { background: #1557b0; }

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
            transition: transform 0.1s, opacity 0.3s;
            border: none;
            box-shadow: 0 4px 10px rgba(0,0,0,0.15);
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* SVG styling for high-contrast white silhouettes */
        .chord-btn SVG {
            width: 65%;
            height: 65%;
            fill: white;
        }

        .chord-btn.locked {
            cursor: not-allowed;
            background-color: rgba(0, 0, 0, 0.05) !important;
            box-shadow: inset 0 2px 8px rgba(0,0,0,0.05);
            opacity: 1;
        }
        
        .chord-btn.locked SVG { display: none; } /* Hide animal when locked */

        .red    { background-color: #ff5252; }
        .brown  { background-color: #8d6e63; }
        .pink   { background-color: #f48fb1; }
        .purple { background-color: #ba68c8; }
        .orange { background-color: #ffb74d; }
        .yellow { background-color: #fff176; }
        .green  { background-color: #81c784; }
        .teal   { background-color: #4db6ac; }
        .grey   { background-color: #b0bec5; }

        #msg { margin-top: 15px; font-size: 1rem; font-weight: 600; color: #636e72; min-height: 1.2rem; text-align: center;}
        #timer-display { font-weight: 700; color: #e74c3c; margin-top: 5px; font-size: 1.1rem; min-height: 1.2rem;}
    </style>
</head>
<body>

    <div class="container">
        <h1>PRODIGIES-EGUCHI</h1>

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

            <button id="play-btn" onclick="startRound()">Start Level</button>
            <div id="msg">Practice Mode: Tap animals to hear them</div>
            <button id="replay-btn" onclick="replaySound()">Replay Sound</button>
            <div id="timer-display"></div>

            <div class="grid">
                <button class="chord-btn red" id="btn-red" onclick="handleInput('red')">
                    <SVG viewBox="0 0 24 24"><path d="M12,2C6.48,2 2,6.48 2,12C2,17.52 6.48,22 12,22C17.52,22 22,17.52 22,12C22,6.48 17.52,2 12,2M12,8.5C13.66,8.5 15,9.84 15,11.5C15,13.16 13.66,14.5 12,14.5C10.34,14.5 9,13.16 9,11.5C9,9.84 10.34,8.5 12,8.5M12,19.25C9.67,19.25 7.64,18.06 6.5,16.25C6.54,14.42 10.17,13.42 12,13.42C13.83,13.42 17.46,14.42 17.5,16.25C16.36,18.06 14.33,19.25 12,19.25Z"></path></SVG>
                </button>
                <button class="chord-btn brown locked" id="btn-brown" onclick="handleInput('brown')">
                    <SVG viewBox="0 0 24 24"><path d="M12,2C14.1,2 15.9,3.8 15.9,5.9C15.9,8 14.1,9.8 12,9.8C9.9,9.8 8.1,8 8.1,5.9C8.1,3.8 9.9,2 12,2M18.8,12.7C19.5,12.7 20,13.2 20,13.9V18.1C20,18.8 19.5,19.3 18.8,19.3H16.1V22H14.1V19.3H9.9V22H7.9V19.3H5.2C4.5,19.3 4,18.8 4,18.1V13.9C4,13.2 4.5,12.7 5.2,12.7H7.3V11.2C7.3,8.7 9.4,6.6 11.9,6.6C14.4,6.6 16.5,8.7 16.5,11.2V12.7H18.8Z"></path></SVG>
                </button>
                <button class="chord-btn pink locked" id="btn-pink" onclick="handleInput('pink')">
                    <SVG viewBox="0 0 24 24"><path d="M19,17V19H17V17H19M15,17V19H13V17H15M11,17V19H9V17H11M7,17V19H5V17H7M19,13V15H17V13H19M15,13V15H13V13H15M11,13V15H9V13H11M7,13V15H5V13H7M19,9V11H17V9H19M15,9V11H13V9H15M11,9V11H9V9H11M7,9V11H5V9H7M19,5V7H17V5H19M15,5V7H13V5H15M11,5V7H9V5H11M7,5V7H5V5H7Z"></path></SVG>
                </button>
                <button class="chord-btn purple locked" id="btn-purple" onclick="handleInput('purple')">
                    <SVG viewBox="0 0 24 24"><path d="M12,12C14.21,12 16,10.21 16,8C16,5.79 14.21,4 12,4C9.79,4 8,5.79 8,8C8,10.21 9.79,12 12,12M12,14C9.33,14 4,15.33 4,18V20H20V18C20,15.33 14.67,14 12,14Z"></path></SVG>
                </button>
                <button class="chord-btn orange locked" id="btn-orange" onclick="handleInput('orange')">
                    <SVG viewBox="0 0 24 24"><path d="M12,2C6.48,2 2,6.48 2,12C2,17.52 6.48,22 12,22C17.52,22 22,17.52 22,12C22,6.48 17.52,2 12,2M12,14.5C10.34,14.5 9,13.16 9,11.5C9,9.84 10.34,8.5 12,8.5C13.66,8.5 15,9.84 15,11.5C15,13.16 13.66,14.5 12,14.5M12,19.25C9.67,19.25 7.64,18.06 6.5,16.25C6.54,14.42 10.17,13.42 12,13.42C13.83,13.42 17.46,14.42 17.5,16.25C16.36,18.06 14.33,19.25 12,19.25Z"></path></SVG>
                </button>
                <button class="chord-btn yellow locked" id="btn-yellow" onclick="handleInput('yellow')">
                    <SVG viewBox="0 0 24 24"><path d="M12,2C14.1,2 15.9,3.8 15.9,5.9C15.9,8 14.1,9.8 12,9.8C9.9,9.8 8.1,8 8.1,5.9C8.1,3.8 9.9,2 12,2M18.8,12.7C19.5,12.7 20,13.2 20,13.9V18.1C20,18.8 19.5,19.3 18.8,19.3H16.1V22H14.1V19.3H9.9V22H7.9V19.3H5.2C4.5,19.3 4,18.8 4,18.1V13.9C4,13.2 4.5,12.7 5.2,12.7H7.3V11.2C7.3,8.7 9.4,6.6 11.9,6.6C14.4,6.6 16.5,8.7 16.5,11.2V12.7H18.8Z"></path></SVG>
                </button>
                <button class="chord-btn green locked" id="btn-green" onclick="handleInput('green')">
                    <SVG viewBox="0 0 24 24"><path d="M19,17V19H17V17H19M15,17V19H13V17H15M11,17V19H9V17H11M7,17V19H5V17H7M19,13V15H17V13H19M15,13V15H13V13H15M11,13V15H9V13H11M7,13V15H5V13H7M19,9V11H17V9H19M15,9V11H13V9H15M11,9V11H9V9H11M7,9V11H5V9H7M19,5V7H17V5H19M15,5V7H13V5H15M11,5V7H9V5H11M7,5V7H5V5H7Z"></path></SVG>
                </button>
                <button class="chord-btn teal locked" id="btn-teal" onclick="handleInput('teal')">
                    <SVG viewBox="0 0 24 24"><path d="M12,12C14.21,12 16,10.21 16,8C16,5.79 14.21,4 12,4C9.79,4 8,5.79 8,8C8,10.21 9.79,12 12,12M12,14C9.33,14 4,15.33 4,18V20H20V18C20,15.33 14.67,14 12,14Z"></path></SVG>
                </button>
                <button class="chord-btn grey locked" id="btn-grey" onclick="handleInput('grey')">
                    <SVG viewBox="0 0 24 24"><path d="M12,2C6.48,2 2,6.48 2,12C2,17.52 6.48,22 12,22C17.52,22 22,17.52 22,12C22,6.48 17.52,2 12,2M12,14.5C10.34,14.5 9,13.16 9,11.5C9,9.84 10.34,8.5 12,8.5C13.66,8.5 15,9.84 15,11.5C15,13.16 13.66,14.5 12,14.5M12,19.25C9.67,19.25 7.64,18.06 6.5,16.25C6.54,14.42 10.17,13.42 12,13.42C13.83,13.42 17.46,14.42 17.5,16.25C16.36,18.06 14.33,19.25 12,19.25Z"></path></SVG>
                </button>
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
        let isGameActive = false;
        let canPractice = true;

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

        function replaySound() {
            if (currentTarget) playSound(currentTarget);
        }

        function startTimer() {
            clearInterval(timerInterval);
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
            document.getElementById('timer-display').innerText = "";
        }

        function updateTimerUI() {
            document.getElementById('timer-display').innerText = timeLeft > 0 ? `Time: ${timeLeft}s` : "";
        }

        function handleMistake(message) {
            strikes++;
            streak = 0;
            currentTarget = null;
            isGameActive = false;
            document.getElementById('replay-btn').style.display = "none";
            
            if (strikes >= 3) {
                alert("3 Strikes! Back to Level 1.");
                strikes = 0;
                activeCount = 1;
                enterPracticeMode("Back to basics. Practice then start.");
            } else {
                document.getElementById('msg').innerText = message;
                document.getElementById('msg').style.color = "#e74c3c";
                setTimeout(nextQuestion, 1500);
            }
            updateUI();
        }

        function enterPracticeMode(message) {
            isGameActive = false;
            canPractice = true;
            document.getElementById('play-btn').style.display = "block";
            document.getElementById('replay-btn').style.display = "none";
            document.getElementById('msg').innerText = message;
            document.getElementById('msg').style.color = "#636e72";
            stopTimer();
        }

        function startRound() {
            canPractice = false;
            document.getElementById('play-btn').style.display = "none";
            nextQuestion();
        }

        function nextQuestion() {
            isGameActive = true;
            const available = progression.slice(0, activeCount);
            currentTarget = available[Math.floor(Math.random() * available.length)];
            
            playSound(currentTarget);
            document.getElementById('msg').innerText = "Which animal made that sound?";
            document.getElementById('msg').style.color = "#636e72";
            document.getElementById('replay-btn').style.display = "block";
            startTimer();
        }

        function handleInput(choice) {
            const btn = document.getElementById(`btn-${choice}`);
            if (btn && btn.classList.contains('locked')) return;

            if (canPractice) {
                playSound(choice);
                return;
            }

            if (isGameActive && currentTarget) {
                checkAnswer(choice);
            }
        }

        function checkAnswer(choice) {
            if (choice === currentTarget) {
                stopTimer();
                isGameActive = false;
                document.getElementById('replay-btn').style.display = "none";
                streak++;
                
                document.getElementById('msg').innerText = "Correct! ✨";
                document.getElementById('msg').style.color = "#2ecc71";
                
                if (streak >= streakGoal && activeCount < progression.length) {
                    activeCount++;
                    streak = 0;
                    updateUI();
                    enterPracticeMode("New animal unlocked! Practice first.");
                } else {
                    updateUI();
                    setTimeout(nextQuestion, 1000);
                }
            } else {
                stopTimer();
                handleMistake("Try again!");
            }
        }

        function updateUI() {
            document.getElementById('streak').innerText = streak;
            document.getElementById('strikes').innerText = strikes;
            document.getElementById('progress-bar').style.width = (streak / streakGoal * 100) + "%";
            
            progression.forEach((color, index) => {
                const btn = document.getElementById(`btn-${color}`);
                if (btn) {
                    if (index < activeCount) {
                        btn.classList.remove('locked');
                    } else {
                        btn.classList.add('locked');
                    }
                }
            });
        }
    </script>
</body>
</html>
