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
  </div>

  <div class="graph-container">
    <canvas id="lineChart2"></canvas>
    <div class="graph-title" id="graphTitle2">Advay</div>
  </div>

  <div class="graph-container">
    <canvas id="lineChart3"></canvas>
    <div class="graph-title" id="graphTitle3">Will</div>
  </div>

  <div>
    <button onclick="depositCurrency()">Deposit</button>
    <button onclick="withdrawCurrency()">Withdraw</button>
    <label for="currencyInput">Amount:</label>
    <input type="number" id="currencyInput" placeholder="Enter amount">
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
      if (inputPasscode === "12345") {
        document.getElementById('controls').style.display = "block";
        alert("Control panel unlocked!");
      } else {
        alert("Incorrect passcode!");
      }
    }

    function closePanel() {
      document.getElementById('controls').style.display = "none";
    }

    function updateGraphName() {
      const selectedGraph = parseInt(document.getElementById('selectedGraph').value);
      const newName = document.getElementById('graphName').value;
      if (newName) {
        document.getElementById(`graphTitle${selectedGraph + 1}`).innerText = newName;
      }
    }

    function updateRefreshInterval() {
      const selectedGraph = parseInt(document.getElementById('selectedGraph').value);
      const newInterval = parseInt(document.getElementById('refreshInterval').value);
      if (newInterval > 0) {
        refreshIntervals[selectedGraph] = newInterval;
        startGraphUpdates();
      }
    }

    function updateRandomness() {
      const selectedGraph = parseInt(document.getElementById('selectedGraph').value);
      const newRange = parseInt(document.getElementById('randomnessRange').value);
      if (newRange >= 0) {
        randomnessRanges[selectedGraph] = newRange;
      }
    }

    function setGraphValue() {
      const selectedGraph = parseInt(document.getElementById('selectedGraph').value);
      const newValue = parseInt(document.getElementById('setValue').value);
      if (!isNaN(newValue)) {
        values[selectedGraph] = newValue;
        peakValues[selectedGraph] = Math.max(peakValues[selectedGraph], newValue);
        dataSets[selectedGraph].data.push(newValue);
        const now = new Date().toLocaleTimeString();
        dataSets[selectedGraph].labels.push(now);
        if (dataSets[selectedGraph].labels.length > 10) {
          dataSets[selectedGraph].labels.shift();
          dataSets[selectedGraph].data.shift();
        }
        lineCharts[selectedGraph].update();
      }
    }

    function depositCurrency() {
      const amount = parseFloat(document.getElementById('currencyInput').value);
      if (amount > 0) {
        currency += amount;
        updateCurrencyDisplay();
      } else {
        alert("Enter a positive amount to deposit!");
      }
    }

    function withdrawCurrency() {
      const amount = parseFloat(document.getElementById('currencyInput').value);
      if (amount > 0) {
        if (amount <= currency) {
          currency -= amount;
          updateCurrencyDisplay();
        } else {
          alert("Not enough funds to withdraw!");
        }
      } else {
        alert("Enter a positive amount to withdraw!");
      }
    }

    function updateCurrencyDisplay() {
      document.getElementById('currencyDisplay').innerText = `Currency: $${currency.toFixed(2)}`;
    }

    // Initialize charts
    initializeChart("lineChart1", "graphTitle1", 0);
    initializeChart("lineChart2", "graphTitle2", 1);
    initializeChart("lineChart3", "graphTitle3", 2);
    startGraphUpdates();
  </script>
</body>
</html>

