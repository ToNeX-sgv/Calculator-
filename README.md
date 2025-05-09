# Calculator-

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no" />
<title>Anthony Calculator</title>
<style>
  /* Reset */
  * {
    box-sizing: border-box;
  }
  body {
    margin: 0;
    background: #121212;
    color: #fff;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    padding: 10px;
  }

  #calculator {
    background: linear-gradient(145deg, #1e1e1e, #292929);
    border-radius: 20px;
    box-shadow: 5px 5px 15px #151515, -5px -5px 15px #383838;
    width: 100%;
    max-width: 350px;
    height: 580px;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  .display {
    background: #222;
    padding: 20px 25px;
    font-size: 2.1rem;
    text-align: right;
    border-radius: 20px 20px 0 0;
    letter-spacing: 1px;
    user-select: none;
    min-height: 90px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    word-wrap: break-word;
  }

  .display small {
    font-size: 1.2rem;
    color: #888;
    min-height: 20px;
    line-height: 20px;
    overflow-wrap: break-word;
  }

  .buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 12px;
    padding: 20px 25px 30px 25px;
    background: #1a1a1a;
    flex-grow: 1;
    border-radius: 0 0 20px 20px;
  }

  button {
    background: #2c2c2c;
    border: none;
    border-radius: 12px;
    box-shadow: inset 2px 2px 5px #1c1c1c,
                inset -2px -2px 5px #3a3a3a;
    color: #eee;
    font-size: 1.6rem;
    font-weight: 600;
    cursor: pointer;
    transition: background 0.2s ease, box-shadow 0.2s ease;
    outline-offset: 2px;
    user-select: none;
  }

  button:active {
    box-shadow: inset -1px -1px 5px #1c1c1c,
                inset 1px 1px 5px #3a3a3a;
    background: #3a3a3a;
  }

  button.operator {
    background: #f57c00;
    color: #fff;
    font-weight: 700;
    box-shadow: 2px 2px 10px #b96900;
  }

  button.operator:active {
    background: #d26900;
    box-shadow: none;
  }

  button.zero {
    grid-column: span 2;
  }

  @media (max-height: 600px) {
    body {
      height: 100vh;
    }
    #calculator {
      height: 600px;
    }
  }
</style>
</head>
<body>
  <div id="calculator" role="application" aria-label="Calculator">
    <div class="display" aria-live="assertive">
      <small id="history"></small>
      <div id="result">0</div>
    </div>
    <div class="buttons">
      <button type="button" class="operator" data-action="clear" aria-label="Clear">C</button>
      <button type="button" class="operator" data-action="backspace" aria-label="Backspace">⌫</button>
      <button type="button" class="operator" data-action="percent" aria-label="Percent">%</button>
      <button type="button" class="operator" data-action="divide" aria-label="Divide">÷</button>

      <button type="button" data-action="7">7</button>
      <button type="button" data-action="8">8</button>
      <button type="button" data-action="9">9</button>
      <button type="button" class="operator" data-action="multiply" aria-label="Multiply">×</button>

      <button type="button" data-action="4">4</button>
      <button type="button" data-action="5">5</button>
      <button type="button" data-action="6">6</button>
      <button type="button" class="operator" data-action="subtract" aria-label="Subtract">−</button>

      <button type="button" data-action="1">1</button>
      <button type="button" data-action="2">2</button>
      <button type="button" data-action="3">3</button>
      <button type="button" class="operator" data-action="add" aria-label="Add">+</button>

      <button type="button" class="zero" data-action="0">0</button>
      <button type="button" data-action="decimal" aria-label="Decimal point">.</button>
      <button type="button" class="operator" data-action="equals" aria-label="Equals">=</button>
    </div>
  </div>

<script>
(() => {
  const historyEl = document.getElementById('history');
  const resultEl = document.getElementById('result');
  const buttons = document.querySelectorAll('.buttons button');

  let currentInput = '0';
  let history = '';
  let resetNext = false;

  function updateDisplay() {
    resultEl.textContent = currentInput;
    historyEl.textContent = history;
  }

  function appendDigit(digit) {
    if (resetNext) {
      currentInput = digit;
      resetNext = false;
    } else if (currentInput === '0') {
      currentInput = digit;
    } else {
      currentInput += digit;
    }
  }

  function appendDecimal() {
    if (resetNext) {
      currentInput = '0.';
      resetNext = false;
    } else if (!currentInput.includes('.')) {
      currentInput += '.';
    }
  }

  function clearAll() {
    currentInput = '0';
    history = '';
    resetNext = false;
  }

  function backspace() {
    if (resetNext) {
      currentInput = '0';
      resetNext = false;
      return;
    }
    if (currentInput.length === 1) {
      currentInput = '0';
    } else {
      currentInput = currentInput.slice(0, -1);
    }
  }

  function applyPercent() {
    let num = parseFloat(currentInput);
    if (!isNaN(num)) {
      num = num / 100;
      currentInput = num.toString();
      resetNext = true;
    }
  }

  function calculate(expression) {
    // sanitize expression to avoid malicious code execution
    // Only allow digits, decimal points, and operators + - * /
    const sanitized = expression.replace(/[^-()\d/*+.]/g, '');
    try {
      // eslint-disable-next-line no-eval
      let result = eval(sanitized);
      if (result === Infinity || result === -Infinity) {
        return 'Error';
      }
      if (Number.isNaN(result)) {
        return 'Error';
      }
      return +result.toFixed(10); // limit to 10 decimals max, strip trailing zeros
    } catch {
      return 'Error';
    }
  }

  function performOperation(operator) {
    if (history && !resetNext) {
      // Chain calculations if possible
      if ("+-*/".includes(history.slice(-1))) {
        history = history.slice(0, -1) + operator;
      } else {
        history += currentInput + operator;
      }
    } else if (resetNext) {
      history = history.slice(0, -1) + operator;
      resetNext = false;
    } else {
      history = currentInput + operator;
      resetNext = true;
    }
    currentInput = '0';
  }

  function resolve() {
    if (!history) {
      return;
    }
    if (!resetNext) {
      history += currentInput;
    } else {
      // If equals pressed consecutively, repeat last operation
      if ("+-*/".includes(history.slice(-1))) {
        // remove trailing operator to avoid error
        history = history.slice(0, -1);
      }
    }
    const result = calculate(history);
    if (result === 'Error') {
      currentInput = 'Error';
      history = '';
    } else {
      currentInput = result.toString();
      history = '';
    }
    resetNext = true;
  }

  buttons.forEach(btn => {
    btn.addEventListener('click', () => {
      const action = btn.dataset.action;
      if (!action) return;

      if (!isNaN(action)) {
        // Digit
        if (currentInput === 'Error') clearAll();
        appendDigit(action);
        updateDisplay();
      } else {
        switch (action) {
          case 'decimal':
            if (currentInput === 'Error') clearAll();
            appendDecimal();
            updateDisplay();
            break;
          case 'clear':
            clearAll();
            updateDisplay();
            break;
          case 'backspace':
            if (currentInput === 'Error') {
              clearAll();
            } else {
              backspace();
            }
            updateDisplay();
            break;
          case 'percent':
            if (currentInput !== 'Error') {
              applyPercent();
              updateDisplay();
            }
            break;
          case 'add':
          case 'subtract':
          case 'multiply':
          case 'divide':
            if (currentInput !== 'Error') {
              let op = '';
              switch(action) {
                case 'add': op = '+'; break;
                case 'subtract': op = '-'; break;
                case 'multiply': op = '*'; break;
                case 'divide': op = '/'; break;
              }
              performOperation(op);
              updateDisplay();
            }
            break;
          case 'equals':
            if (currentInput !== 'Error') {
              resolve();
              updateDisplay();
            }
            break;
        }
      }
    });
  });

  // Keyboard input support
  window.addEventListener('keydown', e => {
    if ((e.key >= '0' && e.key <= '9') || ['.', '+', '-', '*', '/', '%', 'Enter', 'Backspace', 'Delete'].includes(e.key)) {
      e.preventDefault();
      if (e.key >= '0' && e.key <= '9') {
        if (currentInput === 'Error') clearAll();
        appendDigit(e.key);
        updateDisplay();
      } else if (e.key === '.') {
        if (currentInput === 'Error') clearAll();
        appendDecimal();
        updateDisplay();
      } else if (e.key === '+') {
        performOperation('+');
        updateDisplay();
      } else if (e.key === '-') {
        performOperation('-');
        updateDisplay();
      } else if (e.key === '*') {
        performOperation('*');
        updateDisplay();
      } else if (e.key === '/') {
        performOperation('/');
        updateDisplay();
      } else if (e.key === '%') {
        applyPercent();
        updateDisplay();
      } else if (e.key === 'Enter') {
        resolve();
        updateDisplay();
      } else if (e.key === 'Backspace') {
        if (currentInput === 'Error') {
          clearAll();
        } else {
          backspace();
        }
        updateDisplay();
      } else if (e.key === 'Delete') {
        clearAll();
        updateDisplay();
      }
    }
  });

  updateDisplay();
})();
</script>
</body>
</html>

```
