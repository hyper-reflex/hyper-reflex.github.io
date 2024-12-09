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
    }

    header {
      background-color: #4CAF50;
      color: white;
      padding: 20px;
    }

    #graph-container {
      width: 80%;
      margin: 20px auto;
    }

    footer {
      background-color: #f1f1f1;
      padding: 10px;
      position: fixed;
      bottom: 0;
      width: 100%;
    }
  </style>
</head>
<body>
  <header>
    <h1>Number Tracker</h1>
  </header>

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

    // Simulate live data update
    let value = 50; // Starting value
    setInterval(() => {
      const now = new Date().toLocaleTimeString();
      value += Math.floor(Math.random() * 10 - 5); // Random value change
      data.labels.push(now);
      data.datasets[0].data.push(value);

      // Limit to the last 10 data points
      if (data.labels.length > 10) {
        data.labels.shift();
        data.datasets[0].data.shift();
      }

      lineChart.update();
    }, 1000); // Update every second
  </script>
</body>
</html>


