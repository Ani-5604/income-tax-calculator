<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Income Tax Calculator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #c3c4c6;
        }
        .container {
            max-width: 400px;
            margin: 50px auto;
            padding: 20px;
            background-color: #bbf;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
        }
        input[type="number"], button {
            width: calc(100% - 30px); /* Adjust width for icon */
            padding: 10px;
            margin-bottom: 10px;
            background-color: rgb(255, 187, 240);
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            border: 1px solid #cccccc;
            border-radius: 4px;
            box-sizing: border-box;
        }
        .question-mark-container {
            position: relative;
            display: inline-block;
        }
        .question-mark {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            cursor: pointer;
            font-size: 16px;
            color: #888;
            border-radius: 50%;
            background-color: #fff;
            width: 25px;
            height: 25px;
            line-height: 25px;
            text-align: center;
            box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3); 
        }
        .tooltip {
            position: absolute;
            background-color: rgba(0, 0, 0, 0.8);
            color: white;
            border-radius: 4px;
            padding: 10px;
            display: none;
            width: 200px;
            z-index: 1;
        }
        .tooltip::after {
            content: '';
            position: absolute;
            border-style: solid;
            border-width: 5px 5px 0;
            border-color: rgba(0, 0, 0, 0.8) transparent transparent;
            top: 100%;
            left: 50%;
            transform: translateX(-50%);
        }
        input:hover + .question-mark-container .tooltip, .tooltip:hover {
            display: block;
        }
        button[type="button"] {
    background-color: #4CAF50; /* Green */
    color: white;
    padding: 12px 20px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
}

button[type="button"]:hover {
    background-color: #45a049;
}

        /* Modal styles */
        .modal {
            display: none;
            position: fixed;
            z-index: 1;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            overflow: auto;
            background-color: rgba(0,0,0,0.4);
        }
        .modal-content {
            background-color: #fefefe;
            margin: 15% auto;
            padding: 20px;
            border: 1px solid #888;
            width: 80%;
            border-radius: 8px;
            box-shadow: 0 4px 8px 0 rgba(0,0,0,0.2), 0 6px 20px 0 rgba(0,0,0,0.19);
        }
        .close {
            color: #aaa;
            float: right;
            font-size: 28px;
            font-weight: bold;
        }
        .close:hover,
        .close:focus {
            color: black;
            text-decoration: none;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Income Tax Calculator</h1>
        <form id="incomeTaxForm">
            <div>
                <label for="grossIncome">Enter Gross Annual Income (in Lakhs):</label>
                <input type="number" id="grossIncome" min="0" step="0.01" placeholder="Enter your total salary before deductions" required>
                <span class="question-mark-container">
                    <span class="question-mark">?</span>
                    <div class="tooltip">
                        This is your total salary before any deductions or additional income.
                    </div>
                </span>
            </div>
            <br>
            <div>
                <label for="extraIncome">Enter Extra Income from Other Sources (in Lakhs) (if any):</label>
                <input type="number" id="extraIncome" min="0" step="0.01" placeholder="Enter additional income from other sources">
                <span class="question-mark-container">
                    <span class="question-mark">?</span>
                    <div class="tooltip">
                        Any additional income from sources other than your primary salary.
                    </div>
                </span>
            </div>
            <br>
            <label for="ageGroup">Select Age Group:</label>
            <select id="ageGroup" required>
                <option value="below60">Below 60 years</option>
                <option value="60to80">60 to 80 years</option>
                <option value="above80">Above 80 years</option>
            </select>
            <br>
            <div>
                <label for="deductions">Enter Total Applicable Deductions (in Lakhs):</label>
                <input type="number" id="deductions" min="0" step="0.01" placeholder="Enter total applicable deductions">
                <span class="question-mark-container">
                    <span class="question-mark">?</span>
                    <div class="tooltip">
                        Total deductions applicable, reducing your taxable income.
                    </div>
                </span>
            </div>
            <br>
            <button type="button" onclick="calculateTax()">Submit</button>
        </form>
        <div id="resultModal" class="modal">
            <div class="modal-content">
                <span class="close" onclick="closeModal()">&times;</span>
                <p>Total Income: <span id="totalIncomeDisplay"></span></p>
                <p id="taxResult"></p>
            </div>
        </div>
    </div>

    <script>
        function calculateTax() {
            const grossIncome = parseFloat(document.getElementById('grossIncome').value);
            const extraIncome = parseFloat(document.getElementById('extraIncome').value) || 0;
            const ageGroup = document.getElementById('ageGroup').value;
            const deductions = parseFloat(document.getElementById('deductions').value) || 0;

            if (isNaN(grossIncome)) {
                alert('Please enter a valid gross annual income.');
                return;
            }

            const totalIncome = grossIncome + extraIncome;
            const taxableIncome = totalIncome - deductions;

            if (taxableIncome <= 0) {
                displayModal('No tax applicable.', totalIncome);
                return;
            }

            let taxRate;
            switch (ageGroup) {
                case 'below60':
                    taxRate = 0.3; // 30% tax rate for age < 60
                    break;
                case '60to80':
                    taxRate = 0.4; // 40% tax rate for age 60-80
                    break;
                case 'above80':
                    taxRate = 0.1; // 10% tax rate for age > 80
                    break;
                default:
                    taxRate = 0.3; // Default tax rate for age < 60
            }

            const taxAmount = taxableIncome * taxRate;
            displayModal(`Tax Amount: ${taxAmount.toFixed(2)} Lakhs`, totalIncome);
        }

        function displayModal(message, totalIncome) {
            const modal = document.getElementById('resultModal');
            const resultText = document.getElementById('taxResult');
            const totalIncomeDisplay = document.getElementById('totalIncomeDisplay');
            resultText.textContent = message;
            totalIncomeDisplay.textContent = `Total Income: ${totalIncome.toFixed(2)} Lakhs`;
            modal.style.display = 'block';
        }

        function closeModal() {
            const modal = document.getElementById('resultModal');
            modal.style.display = 'none';
        }
    </script>
</body>
</html>
