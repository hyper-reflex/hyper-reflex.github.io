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
      margin: 20px auto;
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

    input[type="number"] {
      padding: 10px;
      font-size: 16px;
      width: 100px;
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
  </style>
</head>
<body>
  <header>
    <h1>Number Tracker</h1>
  </header>

  <div id="controls">
    <label for="manualValue">Set Value: </label>
    <input type="number" id="manualValue" placeholder="Enter value">
    <button onclick="updateValue()">Update</button>
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

    // Simulate live data update
    setInterval(() => {
      const now = new Date().toLocaleTimeString();
      value += Math.floor(Math.random() * 10 - 5); // Random value change
      addDataPoint(now, value);
    }, 1000); // Update every second

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
  </script>
</body>
</html>
