# Zayne
Ear Training
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Absolute Pitch Color Training</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: #f0f4f8;
            margin: 0;
            padding: 20px;
        }

        h1 { color: #333; margin-bottom: 20px; text-transform: uppercase; letter-spacing: 1px; }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
            gap: 15px;
            width: 100%;
            max-width: 900px;
        }

        .chord-btn {
            border: none;
            padding: 25px;
            color: white;
            font-size: 1.1rem;
            font-weight: bold;
            cursor: pointer;
            border-radius: 12px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.1);
            transition: transform 0.1s, box-shadow 0.1s;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 5px;
        }

        .chord-btn:active {
            transform: translateY(2px);
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .notes-label {
            font-size: 0.85rem;
            opacity: 0.9;
            font-weight: normal;
        }

        /* Your Custom Color Palette */
        .red { background-color: #D32F2F; }
        .brown { background-color: #6D4C41; }
        .pink { background-color: #F06292; }
        .purple { background-color: #8E24AA; }
        .orange { background-color: #FB8C00; }
        .yellow { background-color: #FDD835; color: #333; }
        .green { background-color: #43A047; }
        .teal { background-color: #00897B; }
        .grey { background-color: #757575; }

        footer { margin-top: 30px; font-size: 0.8rem; color: #666; }
    </style>
</head>
<body>

    <h1>Chord Sequencing Guide</h1>

    <div class="grid">
        <button class="chord-btn red" onclick="playChord('red')">
            1. C MAJOR <span class="notes-label">Notes: C, E, G</span>
        </button>
        <button class="chord-btn brown" onclick="playChord('brown')">
            2. F MAJOR (2nd Inv) <span class="notes-label">Notes: C, F, A</span>
        </button>
        <button class="chord-btn pink" onclick="playChord('pink')">
            3. G MAJOR (1st Inv) <span class="notes-label">Notes: B, D, G</span>
        </button>
        <button class="chord-btn purple" onclick="playChord('purple')">
            4. F MAJOR (1st Inv) <span class="notes-label">Notes: A, C, F</span>
        </button>
        <button class="chord-btn orange" onclick="playChord('orange')">
            5. G MAJOR (2nd Inv) <span class="notes-label">Notes: D, G, B</span>
        </button>
        <button class="chord-btn yellow" onclick="playChord('yellow')">
            6. C MAJOR (1st Inv) <span class="notes-label">Notes: E, G, C</span>
        </button>
        <button class="chord-btn green" onclick="playChord('green')">
            7. F MAJOR <span class="notes-label">Notes: F, A, C</span>
        </button>
        <button class="chord-btn teal" onclick="playChord('teal')">
            8. G MAJOR <span class="notes-label">Notes: G, B, D</span>
        </button>
        <button class="chord-btn grey" onclick="playChord('grey')">
            9. C MAJOR (2nd Inv) <span class="notes-label">Notes: G, C, E</span>
        </button>
    </div>

    <footer>Based on the Revised Chord Sequencing Guide</footer>

    <script>
        const chordData = {
            'red':    [261.63, 329.63, 392.00], // C4, E4, G4
            'brown':  [261.63, 349.23, 440.00], // C4, F4, A4
            'pink':   [246.94, 293.66, 392.00], // B3, D4, G4
            'purple': [220.00, 261.63, 349.23], // A3, C4, F4
            'orange': [293.66, 392.00, 493.88], // D4, G4, B4
            'yellow': [329.63, 392.00, 523.25], // E4, G4, C5
            'green':  [349.23, 440.00, 523.25], // F4, A4, C5
            'teal':   [392.00, 493.88, 587.33], // G4, B4, D5
            'grey':   [392.00, 523.25, 659.25]  // G4, C5, E5
        };

        let audioCtx = null;

        function playChord(color) {
            if (!audioCtx) {
                audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            }

            const frequencies = chordData[color];
            const now = audioCtx.currentTime;

            frequencies.forEach(freq => {
                const osc = audioCtx.createOscillator();
                const gain = audioCtx.createGain();

                osc.type = 'triangle'; // Gentle tone for children's ears
                osc.frequency.setValueAtTime(freq, now);

                // Quick attack, slow decay for a "bell" effect
                gain.gain.setValueAtTime(0, now);
                gain.gain.linearRampToValueAtTime(0.15, now + 0.05);
                gain.gain.exponentialRampToValueAtTime(0.0001, now + 2.0);

                osc.connect(gain);
                gain.connect(audioCtx.destination);

                osc.start(now);
                osc.stop(now + 2.0);
            });
        }
    </script>
</body>
</html>
```
