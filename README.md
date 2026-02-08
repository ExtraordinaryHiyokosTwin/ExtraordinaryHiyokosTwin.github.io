<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blue Glass Calculator - Regular</title>
    <style>
        /* 1. LOAD REGULAR FONT */
        @font-face {
            font-family: 'SFProRoundedRegular';
            src: url('SF-Pro-Rounded-Regular.otf') format('opentype');
            font-weight: normal;
            font-style: normal;
        }

        /* 2. RESET & BASE STYLES */
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            /* Applying the new Regular font */
            font-family: 'SFProRoundedRegular', -apple-system, BlinkMacSystemFont, sans-serif;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background-image: url('background.jpg');
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
            background-attachment: fixed;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow: hidden;
        }

        /* 3. CALCULATOR CONTAINER */
        .calculator {
            width: 350px;
            height: 700px;
            padding: 20px;
            display: flex;
            flex-direction: column;
            justify-content: flex-end;
            position: relative;
        }

        /* 4. HEADER & DISPLAY */
        .header {
            position: absolute;
            top: 40px;
            left: 20px;
            color: white;
            font-size: 28px;
            display: flex;
            align-items: center;
            gap: 12px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.3);
        }

        .header-icon {
            width: 34px;
            height: 34px;
            object-fit: contain;
        }

        .display {
            width: 100%;
            text-align: right;
            color: white;
            font-size: 85px; /* Slightly adjusted for regular weight */
            margin-bottom: 20px;
            padding-right: 10px;
            height: 110px;
            overflow: hidden;
            text-shadow: 0 4px 10px rgba(0,0,0,0.2);
            white-space: nowrap;
        }

        /* 5. BUTTON STYLING */
        .buttons-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 15px;
        }

        button {
            width: 100%;
            aspect-ratio: 1;
            border: none;
            border-radius: 24px;
            font-size: 30px;
            cursor: pointer;
            position: relative; 
            overflow: hidden;   
            display: flex;
            justify-content: center;
            align-items: center;
            transition: transform 0.1s;
            
            /* Glassmorphism Effect */
            backdrop-filter: blur(15px);
            -webkit-backdrop-filter: blur(15px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.3);
        }

        button:active {
            transform: scale(0.95);
        }

        /* RIPPLE ANIMATION */
        span.ripple {
            position: absolute;
            border-radius: 50%;
            transform: scale(0);
            animation: ripple 600ms linear;
            background-color: rgba(255, 255, 255, 0.3);
            pointer-events: none;
        }

        @keyframes ripple {
            to { transform: scale(4); opacity: 0; }
        }

        /* BUTTON COLORS */
        .btn-num {
            background-color: rgba(255, 255, 255, 0.12);
            color: white;
        }
        .btn-op-top, .btn-op-side {
            background-color: rgba(255, 255, 255, 0.25);
            color: white;
        }
        .btn-clear {
            background-color: rgba(255, 255, 255, 0.85);
            color: #1a1a1a;
        }
        .btn-equal {
            background-color: white;
            color: black;
        }

        /* Ripple color for light buttons */
        .btn-clear span.ripple, .btn-equal span.ripple {
             background-color: rgba(0, 0, 0, 0.2); 
        }

    </style>
</head>
<body>

    <div class="calculator">
        <div class="header">
            <img src="calculator.png" alt="icon" class="header-icon">
            <span>calculator</span>
        </div>

        <div class="display" id="display">0</div>

        <div class="buttons-grid">
            <button class="btn-clear" onclick="clearDisplay()">C</button>
            <button class="btn-op-top" onclick="appendOperator('(')">(</button>
            <button class="btn-op-top" onclick="appendOperator('%')">%</button>
            <button class="btn-op-side" onclick="appendOperator('/')">÷</button>

            <button class="btn-num" onclick="appendNumber('7')">7</button>
            <button class="btn-num" onclick="appendNumber('8')">8</button>
            <button class="btn-num" onclick="appendNumber('9')">9</button>
            <button class="btn-op-side" onclick="appendOperator('*')">×</button>

            <button class="btn-num" onclick="appendNumber('4')">4</button>
            <button class="btn-num" onclick="appendNumber('5')">5</button>
            <button class="btn-num" onclick="appendNumber('6')">6</button>
            <button class="btn-op-side" onclick="appendOperator('-')">−</button>

            <button class="btn-num" onclick="appendNumber('1')">1</button>
            <button class="btn-num" onclick="appendNumber('2')">2</button>
            <button class="btn-num" onclick="appendNumber('3')">3</button>
            <button class="btn-op-side" onclick="appendOperator('+')">+</button>

            <button class="btn-num" onclick="appendNumber('.')">.</button>
            <button class="btn-num" onclick="appendNumber('0')">0</button>
            <button class="btn-num" onclick="deleteLast()">⌫</button>
            <button class="btn-equal" onclick="calculateResult()">=</button>
        </div>
    </div>

    <script>
        const display = document.getElementById('display');
        let currentInput = "";

        function createRipple(event) {
            const button = event.currentTarget;
            const circle = document.createElement("span");
            const diameter = Math.max(button.clientWidth, button.clientHeight);
            const radius = diameter / 2;
            circle.style.width = circle.style.height = `${diameter}px`;
            circle.style.left = `${event.clientX - button.getBoundingClientRect().left - radius}px`;
            circle.style.top = `${event.clientY - button.getBoundingClientRect().top - radius}px`;
            circle.classList.add("ripple");
            const existingRipple = button.getElementsByClassName("ripple")[0];
            if (existingRipple) existingRipple.remove();
            button.appendChild(circle);
        }

        const buttons = document.getElementsByTagName("button");
        for (const button of buttons) { button.addEventListener("click", createRipple); }

        function updateDisplay() { display.innerText = currentInput || "0"; }
        function appendNumber(number) { 
            if (currentInput === "0" && number !== ".") currentInput = number;
            else currentInput += number; 
            updateDisplay(); 
        }
        function appendOperator(op) { 
            if (currentInput === "" && op !== "(") return;
            const lastChar = currentInput.slice(-1);
            if (['+', '-', '*', '/', '%'].includes(lastChar)) currentInput = currentInput.slice(0, -1) + op;
            else currentInput += op;
            updateDisplay(); 
        }
        function clearDisplay() { currentInput = ""; updateDisplay(); }
        function deleteLast() { currentInput = currentInput.toString().slice(0, -1); updateDisplay(); }
        function calculateResult() {
            try {
                let expression = currentInput.replace(/×/g, '*').replace(/÷/g, '/').replace(/−/g, '-');
                let result = eval(expression); 
                currentInput = isFinite(result) ? result.toString() : "Error";
            } catch { currentInput = "Error"; }
            updateDisplay();
        }
    </script>
</body>
</html>
