<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Number Tracker</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
      background-color: #f9f9f9;
    }

    header {
      background-color: #4CAF50;
      color: white;
      padding: 20px;
    }

    #controls {
      display: none; /* Hidden by default */
      margin: 20px auto;
    }

    #controls div {
      margin-bottom: 10px;
    }

    #graph-container {
      width: 80%;
      margin: 20px auto;
      background: white;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      border-radius: 8px;
      padding: 20px;
    }

    footer {
      background-color: #f1f1f1;
      padding: 10px;
      position: fixed;
      bottom: 0;
      width: 100%;
    }

    input[type="number"], input[type="range"], input[type="password"] {
      padding: 10px;
      font-size: 16px;
      margin: 10px;
    }

    button {
      padding: 10px 20px;
      font-size: 16px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }

    button:hover {
      background-color: #45a049;
    }

    label {
      font-weight: bold;
      display: block;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <header>
    <h1>Number Tracker</h1>
  </header>

  <!-- Passcode dialog -->
  <div id="passcodeDialog">
    <label for="passcode">Enter Passcode to Unlock Controls:</label>
    <input type="password" id="passcode" placeholder="Enter passcode">
    <button onclick="checkPasscode()">Unlock</button>
    <p id="errorMessage" style="color: red; display: none;">Incorrect passcode!</p>
  </div>

  <!-- Controls (hidden by default) -->
  <div id="controls">
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
    <canvas id="lineChart"></canvas>
  </div>

  <footer>
    <p>Thank you for visiting our Number Tracker!</p>
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
    const baseChangeElement = document.getElementById('baseChange');
    const randomRangeElement = document.getElementById('randomRange');
    const baseChangeValueDisplay = document.getElementById('baseChangeValue');
    const randomRangeValueDisplay = document.getElementById('randomRangeValue');

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
        addDataPoint(now, value);
        document.getElementById('manualValue').value = ''; // Clear input
      }
    }

    // Unlock controls with passcode
    const passcode = "1234"; // Set your passcode here
    function checkPasscode() {
      const input = document.getElementById('passcode').value;
      if (input === passcode) {
        document.getElementById('passcodeDialog').style.display = "none";
        document.getElementById('controls').style.display = "block";
      } else {
        document.getElementById('errorMessage').style.display = "block";
      }
    }
  </script>
</body>
</html>
