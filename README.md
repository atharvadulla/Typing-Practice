<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🎯 Typing Speed Test</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: #f0f0f5;
            margin: 0;
            padding: 20px;
            text-align: center;
        }
        #test-container {
            background-color: #fff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
            max-width: 600px;
            margin: 20px auto;
        }
        h1 {
            font-size: 28px;
            color: #333;
        }
        #text-display {
            font-size: 18px;
            margin-bottom: 20px;
            padding: 10px;
            background-color: #e6e6e6;
            border-radius: 5px;
            font-weight: bold;
            text-align: left;
            margin-top: 20px;
        }
        #text-input {
            width: 100%;
            padding: 12px;
            font-size: 18px;
            border: 1px solid #ccc;
            border-radius: 5px;
            margin-top: 10px;
            transition: border-color 0.3s;
        }
        #text-input:focus {
            outline: none;
            border-color: #007bff;
        }
        .incorrect {
            border-color: #ff6666;
        }
        #result, #timer, #progress {
            margin-top: 20px;
        }
        #progress-bar {
            width: 100%;
            background-color: #f3f3f3;
            border-radius: 5px;
            margin-top: 10px;
        }
        #progress-bar span {
            display: block;
            height: 10px;
            background-color: #4caf50;
            border-radius: 5px;
        }
        button {
            padding: 12px 24px;
            font-size: 18px;
            margin-top: 20px;
            background-color: #007bff;
            color: #fff;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #0056b3;
        }
        #instructions {
            font-size: 18px;
            margin-bottom: 20px;
            color: #555;
        }
        #time-selection {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div id="test-container">
        <h1>🎯 Typing Speed Test 🚀</h1>
        <div id="instructions">
            <p>Select the time duration for your typing test:</p>
            <div id="time-selection">
                <select id="time-dropdown">
                    <option value="30">30 seconds</option>
                    <option value="60">1 minute</option>
                    <option value="120">2 minutes</option>
                    <option value="180">3 minutes</option>
                </select>
            </div>
        </div>
        <div id="text-display">🐕 The quick brown fox jumps over the lazy dog. 🦊</div>
        <input type="text" id="text-input" placeholder="💻 Start typing..." disabled />
        <div id="timer">Time left: <span id="time-left">30</span> seconds</div>
        <div id="result">WPM: 0</div>
        <div id="progress">
            <div id="progress-bar"><span></span></div>
        </div>
        <button onclick="startTest()">📝 Start Test</button>
        <button onclick="restartTest()">🔄 Restart</button>
    </div>

    <script>
        const textDisplay = document.getElementById('text-display');
        const textInput = document.getElementById('text-input');
        const result = document.getElementById('result');
        const timerDisplay = document.getElementById('time-left');
        const progressBar = document.getElementById('progress-bar').querySelector('span');
        const timeDropdown = document.getElementById('time-dropdown');
        const testText = textDisplay.innerText;
        let startTime, timerInterval;
        let timeLeft;

        // Start the test
        function startTest() {
            timeLeft = parseInt(timeDropdown.value); // Get the selected time from the dropdown
            timerDisplay.innerText = timeLeft;
            textInput.disabled = false;
            textInput.value = '';
            result.innerText = 'WPM: 0';
            progressBar.style.width = '0%';
            textInput.classList.remove('incorrect');
            startTime = null;
            clearInterval(timerInterval);
            startTimer(); // Start the timer
        }

        // Start the timer countdown
        function startTimer() {
            timerInterval = setInterval(() => {
                timeLeft--;
                timerDisplay.innerText = timeLeft;
                if (timeLeft <= 0) {
                    clearInterval(timerInterval);
                    textInput.disabled = true;
                    result.innerText = `⏰ Time's up! Your typing speed is ${Math.round(calculateWPM())} WPM. 🏆`;
                }
            }, 1000);
        }

        // Calculate words per minute
        function calculateWPM() {
            const typedText = textInput.value;
            const words = typedText.split(' ').length;
            const timeElapsed = (new Date().getTime() - startTime) / 1000;
            return (words / timeElapsed) * 60;
        }

        // Handle user input and update UI
        textInput.addEventListener('input', () => {
            if (!startTime) {
                startTime = new Date().getTime();
            }

            const typedText = textInput.value;
            const typedWords = typedText.split(' ').length;

            // Show real-time WPM
            result.innerText = `WPM: ${Math.round(calculateWPM())}`;

            // Check accuracy and update progress bar
            let correctText = testText.substring(0, typedText.length);
            let incorrectText = testText.substring(typedText.length);
            textDisplay.innerHTML = correctText + "<span style='color: #ff6666;'>" + incorrectText + "</span>";

            // Change input field border based on accuracy
            if (typedText === testText.substring(0, typedText.length)) {
                textInput.classList.remove('incorrect');
            } else {
                textInput.classList.add('incorrect');
            }

            // Progress bar update
            let progress = (typedText.length / testText.length) * 100;
            progressBar.style.width = progress + "%";

            if (typedText === testText) {
                clearInterval(timerInterval);
                result.innerText = `🎉 Well done! Your final typing speed is ${Math.round(calculateWPM())} WPM! 🏆`;
                textInput.disabled = true;
            }
        });

        // Restart the test
        function restartTest() {
            timeLeft = 30; // Default time set to 30 seconds
            timerDisplay.innerText = timeLeft;
            textInput.value = '';
            textInput.disabled = false;
            result.innerText = 'WPM: 0';
            progressBar.style.width = '0%';
            textInput.classList.remove('incorrect');
            startTime = null;
            clearInterval(timerInterval);
            startTimer();
            textDisplay.innerHTML = testText; // Reset the text display to the original sentence
        }
    </script>
</body>
</html>
