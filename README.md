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
      background: linear-gradient(to bottom, #606060, #333333); /* Grey gradient */
      color: white;
    }

    header {
      background-color: #444;
      color: white;
      padding: 20px;
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
      color: white;
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
      color: #888;
      margin-bottom: 10px;
    }

    #valueDisplay {
      font-size: 18px;
      margin-top: 20px;
    }

    #statusDisplay {
      margin-top: 5px;
      font-weight: bold;
      color: white;
      padding: 5px;
      display: inline-block;
      border-radius: 5px;
    }

    #statusDisplay.up {
      background-color: #2e7d32;
    }

    #statusDisplay.down {
      background-color: #e53935;
    }
  </style>
</head>
<body>
  <header>
    <h1>Dumb Stocks</h1>
  </header>

  <div>
    <label for="passcodeInput">Enter Passcode:</label>
    <input type="password" id="passcodeInput" placeholder="Enter passcode">
    <button onclick="checkPasscode()">Submit</button>
  </div>

  <div id="controls">
    <div>
      <label for="graphName">Graph Name:</label>
      <input type="text" id="graphName" placeholder="Enter graph name">
      <button onclick="updateGraphName()">Set Name</button>
    </div>
    <div>
      <label for="manualValue">Set Value:</label>
      <input type="number" id="manualValue" placeholder="Enter value">
      <button onclick="updateValue()">Update</button>
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
    // Initialize data for the chart
    const data = {
      labels: [], // Time labels
      datasets: [{
        label: 'Value Over Time',
        data: [], // Values to be tracked
        borderColor: 'rgba(75, 192, 192, 1)',
        backgroundColor: 'rgba(75, 192, 192, 0.2)',
        tension: 0.4
      }]
    };

    // Chart configuration
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

    // Render the chart
    const ctx = document.getElementById('lineChart').getContext('2d');
    const lineChart = new Chart(ctx, config);

    let value = 50; // Starting value
    let peakValue = value;

    const baseChangeElement = document.getElementById('baseChange');
    const randomRangeElement = document.getElementById('randomRange');
    const currentValueElement = document.getElementById('currentValue');
    const statusDisplayElement = document.getElementById('statusDisplay');
    const baseChangeValueDisplay = document.getElementById('baseChangeValue');
    const randomRangeValueDisplay = document.getElementById('randomRangeValue');
    const graphNameElement = document.getElementById('skibidiTitle');

    // Update display for sliders
    baseChangeElement.addEventListener('input', () => {
      baseChangeValueDisplay.textContent = baseChangeElement.value;
    });

    randomRangeElement.addEventListener('input', () => {
      randomRangeValueDisplay.textContent = randomRangeElement.value;
    });

    // Simulate live data update (every 2 seconds)
    setInterval(() => {
      const now = new Date().toLocaleTimeString();
      const baseChange = parseInt(baseChangeElement.value, 10);
      const randomRange = parseInt(randomRangeElement.value, 10);
      const randomChange = Math.floor(Math.random() * (randomRange + 1)) - randomRange / 2;
      value += baseChange + randomChange;

      if (value > peakValue) {
        peakValue = value;
      }

      updateDisplay();
      addDataPoint(now, value);
    }, 2000);

    // Add a data point to the chart
    function addDataPoint(label, newValue) {
      data.labels.push(label);
      data.datasets[0].data.push(newValue);

      // Limit to the last 10 data points
      if (data.labels.length > 10) {
        data.labels.shift();
        data.datasets[0].data.shift();
      }

      lineChart.update();
    }

    // Update value manually
    function updateValue() {
      const manualInput = document.getElementById('manualValue').value;
      if (manualInput !== '') {
        const now = new Date().toLocaleTimeString();
        value = parseInt(manualInput, 10);
        if (value > peakValue) {
          peakValue = value;
        }
        updateDisplay();
        addDataPoint(now, value);
        document.getElementById('manualValue').value = ''; // Clear input
      }
    }

    // Update the current value display and status
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

    // Check passcode
    function checkPasscode() {
      const passcodeInput = document.getElementById('passcodeInput').value;
      if (passcodeInput === "12345") {
        document.getElementById('controls').style.display = 'block';
        alert("Access granted!");
      } else {
        alert("Incorrect passcode. Try again!");
      }
    }

    // Update the graph name
    function updateGraphName() {
      const graphNameInput = document.getElementById('graphName').value;
      if (graphNameInput !== '') {
        graphNameElement.textContent = graphNameInput;
        document.getElementById('graphName').value = ''; // Clear input
      }
    }
  </script>
</body>
</html>
