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
      color: white;
      padding: 20px;
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    #currencyDisplay {
      font-size: 18px;
      margin-right: 20px;
    }

    .graph-container {
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

    input[type="number"], input[type="password"], input[type="text"], button {
      padding: 10px;
      font-size: 16px;
      margin: 5px;
      border-radius: 5px;
      border: none;
    }

    button {
      background-color: #444;
      color: white;
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

    #controls {
      display: none;
      margin: 20px;
    }

    .graph-title {
      font-size: 20px;
      color: white;
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
      <label for="selectedGraph">Select Graph:</label>
      <select id="selectedGraph">
        <option value="0">Krithick</option>
        <option value="1">Advay</option>
        <option value="2">Will</option>
      </select>
    </div>
    <div>
      <label for="graphName">Graph Name:</label>
      <input type="text" id="graphName" placeholder="Enter graph name">
      <button onclick="updateGraphName()">Set Name</button>
    </div>
    <div>
      <label for="refreshInterval">Graph Refresh Interval (ms):</label>
      <input type="number" id="refreshInterval" placeholder="Set interval in ms">
      <button onclick="updateRefreshInterval()">Set Interval</button>
    </div>
    <div>
      <label for="randomnessRange">Randomness Range (±):</label>
      <input type="number" id="randomnessRange" placeholder="Set randomness range">
      <button onclick="updateRandomness()">Set Range</button>
    </div>
    <div>
      <label for="setValue">Set Graph Value:</label>
      <input type="number" id="setValue" placeholder="Enter value">
      <button onclick="setGraphValue()">Set Value</button>
    </div>
  </div>

  <div class="graph-container">
    <canvas id="lineChart1"></canvas>
    <div class="graph-title" id="graphTitle1">Krithick</div>
    <div>
      <button onclick="depositCurrency(0)">Deposit</button>
      <button onclick="withdrawCurrency(0)">Withdraw</button>
      <label for="currencyInput1">Amount:</label>
      <input type="number" id="currencyInput1" placeholder="Enter amount">
    </div>
  </div>

  <div class="graph-container">
    <canvas id="lineChart2"></canvas>
    <div class="graph-title" id="graphTitle2">Advay</div>
    <div>
      <button onclick="depositCurrency(1)">Deposit</button>
      <button onclick="withdrawCurrency(1)">Withdraw</button>
      <label for="currencyInput2">Amount:</label>
      <input type="number" id="currencyInput2" placeholder="Enter amount">
    </div>
  </div>

  <div class="graph-container">
    <canvas id="lineChart3"></canvas>
    <div class="graph-title" id="graphTitle3">Will</div>
    <div>
      <button onclick="depositCurrency(2)">Deposit</button>
      <button onclick="withdrawCurrency(2)">Withdraw</button>
      <label for="currencyInput3">Amount:</label>
      <input type="number" id="currencyInput3" placeholder="Enter amount">
    </div>
  </div>

  <footer>
    <p>Thank you for visiting our Dumb Stocks Tracker!</p>
  </footer>

  <script>
    let currency = 50;
    let intervalIds = [null, null, null];
    let refreshIntervals = [2000, 2000, 2000]; // Default refresh intervals
    let randomnessRanges = [10, 10, 10]; // Default randomness ranges
    let values = [50, 50, 50]; // Starting values
    let peakValues = [...values];

    const dataSets = [
      { labels: [], data: [] },
      { labels: [], data: [] },
      { labels: [], data: [] }
    ];

    const lineCharts = [];

    function initializeChart(canvasId, titleId, index) {
      const ctx = document.getElementById(canvasId).getContext('2d');
      const chartData = {
        labels: [],
        datasets: [{
          label: `Graph ${index + 1}`,
          data: [],
          borderColor: 'rgba(75, 192, 192, 1)',
          backgroundColor: 'rgba(75, 192, 192, 0.2)',
          tension: 0.4
        }]
      };

      const config = {
        type: 'line',
        data: chartData,
        options: {
          responsive: true,
          plugins: { legend: { display: true } },
          scales: {
            x: { title: { display: true, text: 'Time' } },
            y: { title: { display: true, text: 'Value' } }
          }
        }
      };

      const chart = new Chart(ctx, config);
      dataSets[index] = chartData;
      lineCharts[index] = chart;
      document.getElementById(titleId).innerText = `Graph ${index + 1}`;
      return chart;
    }

    function updateGraph(index) {
      const now = new Date().toLocaleTimeString();
      const randomChange = Math.floor(Math.random() * randomnessRanges[index] * 2 - randomnessRanges[index]);
      values[index] += randomChange;

      if (values[index] > peakValues[index]) peakValues[index] = values[index];

      dataSets[index].labels.push(now);
      dataSets[index].data.push(values[index]);
      if (dataSets[index].labels.length > 10) {
        dataSets[index].labels.shift();
        dataSets[index].data.shift();
      }
      lineCharts[index].update();
    }

    function startGraphUpdates() {
      intervalIds.forEach((id, index) => {
        if (id) clearInterval(id);
        intervalIds[index] = setInterval(() => updateGraph(index), refreshIntervals[index]);
      });
    }

    function checkPasscode() {
      const inputPasscode = document.getElementById('passcodeInput').value;
      if (inputPasscode === "12345") {        document.getElementById('controls').style.display = 'block';
        document.getElementById('passcodeInput').value = '';
      } else {
        alert('Incorrect passcode. Please try again.');
      }
    }

    function closePanel() {
      document.getElementById('controls').style.display = 'none';
    }

    function updateGraphName() {
      const selectedIndex = document.getElementById('selectedGraph').value;
      const newName = document.getElementById('graphName').value;
      if (newName) {
        document.getElementById(`graphTitle${parseInt(selectedIndex) + 1}`).innerText = newName;
      }
    }

    function updateRefreshInterval() {
      const selectedIndex = document.getElementById('selectedGraph').value;
      const newInterval = parseInt(document.getElementById('refreshInterval').value, 10);
      if (!isNaN(newInterval) && newInterval > 0) {
        refreshIntervals[selectedIndex] = newInterval;
        clearInterval(intervalIds[selectedIndex]);
        intervalIds[selectedIndex] = setInterval(() => updateGraph(selectedIndex), newInterval);
      }
    }

    function updateRandomness() {
      const selectedIndex = document.getElementById('selectedGraph').value;
      const newRange = parseInt(document.getElementById('randomnessRange').value, 10);
      if (!isNaN(newRange) && newRange >= 0) {
        randomnessRanges[selectedIndex] = newRange;
      }
    }

    function setGraphValue() {
      const selectedIndex = document.getElementById('selectedGraph').value;
      const newValue = parseInt(document.getElementById('setValue').value, 10);
      if (!isNaN(newValue)) {
        values[selectedIndex] = newValue;
        peakValues[selectedIndex] = newValue; // Reset peak to the new value
        dataSets[selectedIndex].labels = [];
        dataSets[selectedIndex].data = [];
        lineCharts[selectedIndex].update();
      }
    }

    function depositCurrency(index) {
      const amount = parseInt(document.getElementById(`currencyInput${index + 1}`).value, 10);
      if (!isNaN(amount) && amount > 0) {
        currency += amount;
        document.getElementById('currencyDisplay').innerText = `Currency: $${currency}`;
      }
    }

    function withdrawCurrency(index) {
      const amount = parseInt(document.getElementById(`currencyInput${index + 1}`).value, 10);
      if (!isNaN(amount) && amount > 0 && currency >= amount) {
        currency -= amount;
        document.getElementById('currencyDisplay').innerText = `Currency: $${currency}`;
      } else {
        alert('Insufficient funds or invalid amount.');
      }
    }

    // Initialize charts when the page loads
    document.addEventListener('DOMContentLoaded', () => {
      initializeChart('lineChart1', 'graphTitle1', 0);
      initializeChart('lineChart2', 'graphTitle2', 1);
      initializeChart('lineChart3', 'graphTitle3', 2);
      startGraphUpdates();
    });
  </script>
</body>
</html>
