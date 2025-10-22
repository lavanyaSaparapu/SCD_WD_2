
import IPython.display as display

html_code = """
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Colorful Calculator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            padding: 20px;
        }

        .calculator {
            background: #2c3e50;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            width: 320px;
            padding: 25px;
        }

        .display {
            background: #34495e;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
            text-align: right;
            box-shadow: inset 0 2px 5px rgba(0, 0, 0, 0.2);
        }

        .previous-operand {
            color: rgba(255, 255, 255, 0.7);
            font-size: 1.2rem;
            min-height: 1.5rem;
            margin-bottom: 5px;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .current-operand {
            color: white;
            font-size: 2.2rem;
            font-weight: 600;
            min-height: 2.5rem;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .buttons-grid {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            grid-gap: 15px;
        }

        button {
            border: none;
            border-radius: 10px;
            padding: 18px 0;
            font-size: 1.3rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s ease;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }

        button:active {
            transform: translateY(3px);
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
        }

        .number {
            background: #3498db;
            color: white;
        }

        .number:hover {
            background: #2980b9;
        }

        .operator {
            background: #e74c3c;
            color: white;
        }

        .operator:hover {
            background: #c0392b;
        }

        .equals {
            background: #2ecc71;
            color: white;
        }

        .equals:hover {
            background: #27ae60;
        }

        .clear, .delete {
            background: #f39c12;
            color: white;
        }

        .clear:hover, .delete:hover {
            background: #d35400;
        }

        .span-two {
            grid-column: span 2;
        }

        .error {
            color: #e74c3c;
        }

        footer {
            margin-top: 20px;
            text-align: center;
            color: white;
            font-size: 0.9rem;
            opacity: 0.8;
        }
    </style>
</head>
<body>
    <div class="calculator">
        <div class="display">
            <div class="previous-operand"></div>
            <div class="current-operand">0</div>
        </div>
        <div class="buttons-grid">
            <button class="clear span-two">AC</button>
            <button class="delete">DEL</button>
            <button class="operator" data-operation="÷">÷</button>
            <button class="number" data-number="7">7</button>
            <button class="number" data-number="8">8</button>
            <button class="number" data-number="9">9</button>
            <button class="operator" data-operation="×">×</button>
            <button class="number" data-number="4">4</button>
            <button class="number" data-number="5">5</button>
            <button class="number" data-number="6">6</button>
            <button class="operator" data-operation="-">-</button>
            <button class="number" data-number="1">1</button>
            <button class="number" data-number="2">2</button>
            <button class="number" data-number="3">3</button>
            <button class="operator" data-operation="+">+</button>
            <button class="number span-two" data-number="0">0</button>
            <button class="number" data-number=".">.</button>
            <button class="equals" data-operation="=">=</button>
        </div>
        <footer>
            <p>Use keyboard for faster calculations!</p>
        </footer>
    </div>

    <script>
        class Calculator {
            constructor(previousOperandElement, currentOperandElement) {
                this.previousOperandElement = previousOperandElement;
                this.currentOperandElement = currentOperandElement;
                this.clear();
            }

            clear() {
                this.currentOperand = '0';
                this.previousOperand = '';
                this.operation = undefined;
                this.hasError = false;
            }

            delete() {
                if (this.hasError) {
                    this.clear();
                    return;
                }

                if (this.currentOperand.length === 1) {
                    this.currentOperand = '0';
                } else {
                    this.currentOperand = this.currentOperand.slice(0, -1);
                }
            }

            appendNumber(number) {
                if (this.hasError) {
                    this.clear();
                }

                if (number === '.' && this.currentOperand.includes('.')) {
                    return;
                }

                if (this.currentOperand === '0' && number !== '.') {
                    this.currentOperand = number;
                } else {
                    this.currentOperand += number;
                }
            }

            chooseOperation(operation) {
                if (this.hasError) {
                    return;
                }

                if (this.currentOperand === '') {
                    return;
                }

                if (this.previousOperand !== '') {
                    this.compute();
                }

                this.operation = operation;
                this.previousOperand = `${this.currentOperand} ${operation}`;
                this.currentOperand = '';
            }

            compute() {
                if (this.hasError) {
                    return;
                }

                let computation;
                const prev = parseFloat(this.previousOperand);
                const current = parseFloat(this.currentOperand);

                if (isNaN(prev) || isNaN(current)) {
                    return;
                }

                try {
                    switch (this.operation) {
                        case '+':
                            computation = prev + current;
                            break;
                        case '-':
                            computation = prev - current;
                            break;
                        case '×':
                            computation = prev * current;
                            break;
                        case '÷':
                            if (current === 0) {
                                throw new Error("Division by zero");
                            }
                            computation = prev / current;
                            break;
                        default:
                            return;
                    }

                    this.currentOperand = computation.toString();
                    this.operation = undefined;
                    this.previousOperand = '';
                    this.hasError = false;
                } catch (error) {
                    this.currentOperand = 'Error';
                    this.previousOperand = '';
                    this.operation = undefined;
                    this.hasError = true;
                }
            }

            getDisplayNumber(number) {
                if (this.hasError) {
                    return number;
                }

                const stringNumber = number.toString();
                const integerDigits = parseFloat(stringNumber.split('.')[0]);
                const decimalDigits = stringNumber.split('.')[1];

                let integerDisplay;

                if (isNaN(integerDigits)) {
                    integerDisplay = '';
                } else {
                    integerDisplay = integerDigits.toLocaleString('en', {
                        maximumFractionDigits: 0
                    });
                }

                if (decimalDigits != null) {
                    return `${integerDisplay}.${decimalDigits}`;
                } else {
                    return integerDisplay;
                }
            }

            updateDisplay() {
                this.currentOperandElement.innerText = this.getDisplayNumber(this.currentOperand);

                if (this.operation != null) {
                    this.previousOperandElement.innerText = `${this.getDisplayNumber(parseFloat(this.previousOperand))} ${this.operation}`;
                } else {
                    this.previousOperandElement.innerText = this.previousOperand;
                }

                if (this.hasError) {
                    this.currentOperandElement.classList.add('error');
                } else {
                    this.currentOperandElement.classList.remove('error');
                }
            }
        }

        // DOM Elements
        const numberButtons = document.querySelectorAll('[data-number]');
        const operationButtons = document.querySelectorAll('[data-operation]');
        const equalsButton = document.querySelector('[data-operation="="]');
        const deleteButton = document.querySelector('.delete');
        const clearButton = document.querySelector('.clear');
        const previousOperandElement = document.querySelector('.previous-operand');
        const currentOperandElement = document.querySelector('.current-operand');

        // Create calculator instance
        const calculator = new Calculator(previousOperandElement, currentOperandElement);

        // Event Listeners for buttons
        numberButtons.forEach(button => {
            button.addEventListener('click', () => {
                calculator.appendNumber(button.innerText);
                calculator.updateDisplay();
            });
        });

        operationButtons.forEach(button => {
            if (button.dataset.operation !== '=') {
                button.addEventListener('click', () => {
                    calculator.chooseOperation(button.innerText);
                    calculator.updateDisplay();
                });
            }
        });

        equalsButton.addEventListener('click', () => {
            calculator.compute();
            calculator.updateDisplay();
        });

        clearButton.addEventListener('click', () => {
            calculator.clear();
            calculator.updateDisplay();
        });

        deleteButton.addEventListener('click', () => {
            calculator.delete();
            calculator.updateDisplay();
        });

        // Keyboard support
        document.addEventListener('keydown', event => {
            if (event.key >= '0' && event.key <= '9') {
                calculator.appendNumber(event.key);
                calculator.updateDisplay();
            }

            if (event.key === '.') {
                calculator.appendNumber(event.key);
                calculator.updateDisplay();
            }

            if (event.key === '+' || event.key === '-' || event.key === '*' || event.key === '/') {
                let operation;
                switch (event.key) {
                    case '+': operation = '+'; break;
                    case '-': operation = '-'; break;
                    case '*': operation = '×'; break;
                    case '/': operation = '÷'; break;
                }
                calculator.chooseOperation(operation);
                calculator.updateDisplay();
            }

            if (event.key === 'Enter' || event.key === '=') {
                calculator.compute();
                calculator.updateDisplay();
            }

            if (event.key === 'Backspace') {
                calculator.delete();
                calculator.updateDisplay();
            }

            if (event.key === 'Escape') {
                calculator.clear();
                calculator.updateDisplay();
            }
        });
    </script>
</body>
</html>
"""

display.display(display.HTML(html_code))
