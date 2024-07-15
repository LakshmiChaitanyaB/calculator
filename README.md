/html/
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculator</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div class="calculator">
        <input type="text" class="calculator-screen" disabled />
        <div class="calculator-keys">
            <button type="button" value="7">7</button>
            <button type="button" value="8">8</button>
            <button type="button" value="9">9</button>
            <button type="button" class="operator" value="+">+</button>

            <button type="button" value="4">4</button>
            <button type="button" value="5">5</button>
            <button type="button" value="6">6</button>
            <button type="button" class="operator" value="-">-</button>

            <button type="button" value="1">1</button>
            <button type="button" value="2">2</button>
            <button type="button" value="3">3</button>
            <button type="button" class="operator" value="*">&times;</button>

            <button type="button" class="all-clear" value="all-clear">AC</button>
            <button type="button" value="0">0</button>
            <button type="button" class="decimal" value=".">.</button>
            <button type="button" class="operator" value="/">&divide;</button>
            <button type="button" class="equal-sign operator" value="=">=</button>
        </div>
    </div>
    <script src="script.js"></script>
</body>
</html>
//css//
body {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #e0f7fa;
    font-family: 'Roboto', sans-serif;
}

.calculator {
    border-radius: 10px;
    box-shadow: 0px 0px 20px 0px #000;
}

.calculator-screen {
    width: 100%;
    height: 80px;
    background-color: #0288d1;
    color: #fff;
    border: none;
    text-align: right;
    padding: 20px;
    box-sizing: border-box;
    font-size: 2.5em;
    border-radius: 10px 10px 0 0;
}

.calculator-keys {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
    padding: 20px;
    background-color: #b3e5fc;
    border-radius: 0 0 10px 10px;
}

button {
    height: 60px;
    font-size: 1.5em;
    border-radius: 10px;
    border: none;
    background-color: #4fc3f7;
    color: #fff;
    box-shadow: 0 3px #0288d1;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

button.operator {
    background-color: #0288d1;
    color: #fff;
}

button.equal-sign {
    background-color: #01579b;
    color: #fff;
    grid-column: span 2;
}

button.all-clear {
    background-color: #0288d1;
    color: #fff;
}

button.decimal {
    background-color: #4fc3f7;
    color: #fff;
}

button:active {
    box-shadow: 0 1px #0288d1;
    transform: translateY(2px);
}

button:focus {
    outline: none;
}
//javascript//
const calculator = {
    displayValue: '0',
    firstOperand: null,
    waitingForSecondOperand: false,
    operator: null,
};

function inputDigit(digit) {
    const { displayValue, waitingForSecondOperand } = calculator;

    if (waitingForSecondOperand === true) {
        calculator.displayValue = digit;
        calculator.waitingForSecondOperand = false;
    } else {
        calculator.displayValue = displayValue === '0' ? digit : displayValue + digit;
    }
}

function inputDecimal(dot) {
    if (calculator.waitingForSecondOperand === true) return;

    if (!calculator.displayValue.includes(dot)) {
        calculator.displayValue += dot;
    }
}

function handleOperator(nextOperator) {
    const { firstOperand, displayValue, operator } = calculator;
    const inputValue = parseFloat(displayValue);

    if (operator && calculator.waitingForSecondOperand) {
        calculator.operator = nextOperator;
        return;
    }

    if (firstOperand == null && !isNaN(inputValue)) {
        calculator.firstOperand = inputValue;
    } else if (operator) {
        const result = performCalculation[operator](firstOperand, inputValue);

        calculator.displayValue = `${parseFloat(result.toFixed(7))}`;
        calculator.firstOperand = result;
    }

    calculator.waitingForSecondOperand = true;
    calculator.operator = nextOperator;
}

const performCalculation = {
    '/': (firstOperand, secondOperand) => firstOperand / secondOperand,
    '*': (firstOperand, secondOperand) => firstOperand * secondOperand,
    '+': (firstOperand, secondOperand) => firstOperand + secondOperand,
    '-': (firstOperand, secondOperand) => firstOperand - secondOperand,
    '=': (firstOperand, secondOperand) => secondOperand
};

function resetCalculator() {
    calculator.displayValue = '0';
    calculator.firstOperand = null;
    calculator.waitingForSecondOperand = false;
    calculator.operator = null;
}

function updateDisplay() {
    const display = document.querySelector('.calculator-screen');
    display.value = calculator.displayValue;
}

updateDisplay();

const keys = document.querySelector('.calculator-keys');
keys.addEventListener('click', (event) => {
    const { target } = event;
    if (!target.matches('button')) {
        return;
    }

    if (target.classList.contains('operator')) {
        handleOperator(target.value);
        updateDisplay();
        return;
    }

    if (target.classList.contains('decimal')) {
        inputDecimal(target.value);
        updateDisplay();
        return;
    }

    if (target.classList.contains('all-clear')) {
        resetCalculator();
        updateDisplay();
        return;
    }

    inputDigit(target.value);
    updateDisplay();
});
