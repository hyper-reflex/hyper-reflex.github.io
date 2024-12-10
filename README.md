<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Dumb Stocks</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: 'Arial', sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
      background-color: #d3d3d3; /* Grey background */
      color: black; /* Black text */
    }

    header {
      background-color: #444;
      color: black; /* Black text */
      padding: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    #currencyDisplay {
      font-size: 18px;
      color: black;
      margin-right: 20px;
    }

    #controls {
      display: none; /* Hidden by default */
      margin: 20px auto;
      padding: 15px;
      background-color: #222;
      border-radius: 8px;
    }

    #controls div {
      margin-bottom: 10px;
    }

    #graph-container {
      width: 80%;
      margin: 20px auto;
      background: #555;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
      border-radius: 8px;
      padding: 20px;
    }

    footer {
      background-color: #222;
      color: #ddd;
      padding: 10px;
      position: fixed;
      bottom: 0;
      width: 100%;
    }

    input[type="number"], input[type="range"], input[type="password"], input[type="text"] {
      padding: 10px;
      font-size: 16px;
      margin: 10px;
      border-radius: 5px;
      border: none;
    }

    button {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #444;
      color: black; /* Black text */
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    button:hover {
      background-color: #333;
    }

    label {
      font-weight: bold;
      display: block;
      margin-top: 10px;
    }

    #skibidiTitle {
      font-size: 24px;
      font-weight: bold;
      color: #444; /* Darker text */
      margin-bottom: 10px;
    }

    #valueDisplay {
      font-size: 18px;
      margin-top: 20px;
    }

    #statusDisplay {
      margin-top: 5px;
      font-weight: bold;
      color: black; /* Black text */
      padding: 5px;
      display: inline-block;
      border-radius: 5px;
    }

    #statusDisplay.up {
      background-color: #2e7d32; /* Green */
    }

    #statusDisplay.down {
      background-color: #e53935; /* Red */
    }
  </style>
</head>
<body>
  <header>
    <h1>Dumb Stocks</h1>
    <div id="currencyDisplay">Currency: $50</div>
  </header>

  <div>
    <label for="passcodeInput">Enter Passcode:</label>
    <input type="password" id="passcodeInput" placeholder="Enter passcode">
    <button onclick="checkPasscode()">Submit</button>
  </div>

  <div id="controls">
    <div>
      <button onclick="closePanel()">Close Panel</button>
    </div>
    <div>
      <label for="graphName">Graph Name:</label>
      <input type="text" id="graphName" placeholder="Enter graph name">
      <button onclick="updateGraphName()">Set Name</button>
    </div>
    <div>
      <label for="depositAmount">Deposit Amount:</label>
      <input type="number" id="depositAmount" placeholder="Amount to invest">
      <button onclick="deposit()">Deposit</button>
    </div>
    <div>
      <label for="withdrawAmount">Withdraw Amount:</label>
      <button onclick="withdraw()">Withdraw</button>
    </div>
    <div>
      <label for="baseChange">Base Increment/Decrement:</label>
      <input type="range" id="baseChange" min="-10" max="10" step="1" value="0">
      <span id="baseChangeValue">0</span>
    </div>
    <div>
      <label for="randomRange">Randomness Range:</label>
      <input type="range" id="randomRange" min="0" max="20" step="1" value="5">
      <span id="randomRangeValue">5</span>
    </div>
    <div>
      <label for="updateInterval">Update Interval (ms):</label>
      <input type="number" id="updateInterval" placeholder="Interval in milliseconds" value="2000">
      <button onclick="changeUpdateInterval()">Set Interval</button>
    </div>
  </div>

  <div id="graph-container">
    <div id="skibidiTitle">Skibidi</div>
    <canvas id="lineChart"></canvas>
    <div id="valueDisplay">
      Current Value: <span id="currentValue">50</span>
      <div id="statusDisplay" class="up">Status: Up</div>
    </div>
  </div>

  <footer>
    <p>Thank you for visiting our Dumb Stocks Tracker!</p>
  </footer>

  <script>
    let value = 50; // Starting value
    let peakValue = value;
    let updateInterval = 2000; // Default update interval
    let intervalId;
    let currency = 50; // Starting currency
    let investment = 0; // Tracks the invested amount

    const currentValueElement = document.getElementById('currentValue');
    const statusDisplayElement = document.getElementById('statusDisplay');
    const currencyDisplay = document.getElementById('currencyDisplay');

    function updateCurrencyDisplay() {
      currencyDisplay.textContent = `Currency: $${currency}`;
    }

    function updateDisplay() {
      currentValueElement.textContent = value;
      if (value < peakValue) {
        statusDisplayElement.textContent = `Status: Down`;
        statusDisplayElement.className = "down";
      } else {
        statusDisplayElement.textContent = `Status: Up`;
        statusDisplayElement.className = "up";
      }
    }

    function deposit() {
      const depositInput = document.getElementById('depositAmount').value;
      const depositAmount = parseFloat(depositInput);
      if (!isNaN(depositAmount) && depositAmount > 0 && depositAmount <= currency) {
        currency -= depositAmount;
        investment += depositAmount / value; // Store units of investment based on graph value
        updateCurrencyDisplay();
        alert(`Successfully invested $${depositAmount}.`);
      } else {
        alert('Invalid deposit amount or insufficient funds.');
      }
    }

    function withdraw() {
      if (investment > 0) {
        const payout = investment * value; // Calculate payout based on current value
        investment = 0; // Reset investment
        currency += payout;
        updateCurrencyDisplay();
        alert(`Successfully withdrew $${payout.toFixed(2)}.`);
      } else {
        alert('No investment to withdraw.');
      }
    }

    function updateChart() {
      const now = new Date().toLocaleTimeString();
      const baseChange = parseInt(document.getElementById('baseChange').value, 10);
      const randomRange = parseInt(document.getElementById('randomRange').value, 10);
      const randomChange = Math.floor(Math.random() * (randomRange + 1)) - randomRange / 2;
      value += baseChange + randomChange;

      if (value > peakValue) peakValue = value;
      updateDisplay();
      addDataPoint(now, value);
    }

    function addDataPoint(label, newValue) {
      data.labels.push(label);
      data.datasets[0].data.push(newValue);
      if (data.labels.length > 10) {
        data.labels.shift();
        data.datasets[0].data.shift();
      }
      lineChart.update();
    }

    const data = {
      labels: [],
      datasets: [{
        label: 'Value Over Time',
        data: [],
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.4
      }]
    };

    const config = {
      type: 'line',
      data: data,
      options: {
        responsive: true,
        plugins: {
          legend: {
            display: true
          }
        },
        scales: {
          x: {
            title: {
              display: true,
              text: 'Time'
            }
          },
          y: {
            title: {
              display: true,
              text: 'Value'
            }
          }
        }
      }
    };

    const ctx = document.getElementById('lineChart').getContext('2d');
    const lineChart = new Chart(ctx, config);

    intervalId = setInterval(updateChart, updateInterval);
  </script>
</body>
</html>
