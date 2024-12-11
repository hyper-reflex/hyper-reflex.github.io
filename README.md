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
        }
        .chart-container {
            display: none;
            margin: 20px auto;
            max-width: 800px;
        }
        .controls {
            margin-bottom: 20px;
        }
    </style>
</head>
<body>
    <div class="controls">
        <button onclick="showGraph(1)">Edit Graph 1</button>
        <button onclick="showGraph(2)">Edit Graph 2</button>
        <button onclick="showGraph(3)">Edit Graph 3</button>
    </div>

    <div>
        <label for="depositAmount">Amount:</label>
        <input type="number" id="depositAmount" placeholder="Enter amount" />
        <button onclick="deposit()">Deposit</button>
        <button onclick="withdraw()">Withdraw</button>
    </div>

    <div class="chart-container" id="chart1">
        <h2>Graph 1</h2>
        <canvas id="lineChart1"></canvas>
        <p id="currencyDisplay1">Currency: $1000</p>
    </div>
    <div class="chart-container" id="chart2">
        <h2>Graph 2</h2>
        <canvas id="lineChart2"></canvas>
        <p id="currencyDisplay2">Currency: $1000</p>
    </div>
    <div class="chart-container" id="chart3">
        <h2>Graph 3</h2>
        <canvas id="lineChart3"></canvas>
        <p id="currencyDisplay3">Currency: $1000</p>
    </div>

    <script>
        let currency1 = 1000, currency2 = 1000, currency3 = 1000; // Initial currency for each graph
        let value1 = 10, value2 = 20, value3 = 15; // Initial stock value for each graph
        let currentGraph = 1;
        let intervalId;
        const refreshInterval = 5000; // Refresh interval for the chart updates

        function updateDisplay() {
            document.getElementById('currencyDisplay1').textContent = `$${currency1}`;
            document.getElementById('currencyDisplay2').textContent = `$${currency2}`;
            document.getElementById('currencyDisplay3').textContent = `$${currency3}`;
        }

        function deposit() {
            const amount = parseInt(document.getElementById('depositAmount').value);
            if (!isNaN(amount) && amount > 0) {
                switch (currentGraph) {
                    case 1:
                        currency1 += amount;
                        alert(`Deposited $${amount} to Graph 1.`);
                        break;
                    case 2:
                        currency2 += amount;
                        alert(`Deposited $${amount} to Graph 2.`);
                        break;
                    case 3:
                        currency3 += amount;
                        alert(`Deposited $${amount} to Graph 3.`);
                        break;
                }
                updateDisplay();
            } else {
                alert("Please enter a valid positive deposit amount.");
            }
        }

        function withdraw() {
            const amount = parseInt(document.getElementById('depositAmount').value);
            if (!isNaN(amount) && amount > 0) {
                if (currentGraph === 1 && amount <= currency1) {
                    currency1 -= amount;
                    alert(`Withdrew $${amount} from Graph 1.`);
                } else if (currentGraph === 2 && amount <= currency2) {
                    currency2 -= amount;
                    alert(`Withdrew $${amount} from Graph 2.`);
                } else if (currentGraph === 3 && amount <= currency3) {
                    currency3 -= amount;
                    alert(`Withdrew $${amount} from Graph 3.`);
                } else {
                    alert("Insufficient funds or invalid input.");
                    return;
                }
                updateDisplay();
            } else {
                alert("Input must be greater than zero!");
            }
        }

        const data1 = { labels: [], datasets: [{ label: 'Graph 1 Value', data: [], borderColor: 'rgba(75, 192, 192, 1)', borderWidth: 1, fill: false }] };
        const data2 = { labels: [], datasets: [{ label: 'Graph 2 Value', data: [], borderColor: 'rgba(54, 162, 235, 1)', borderWidth: 1, fill: false }] };
        const data3 = { labels: [], datasets: [{ label: 'Graph 3 Value', data: [], borderColor: 'rgba(255, 159, 64, 1)', borderWidth: 1, fill: false }] };

        const config1 = { type: 'line', data: data1, options: { scales: { x: { title: { display: true, text: 'Time' } }, y: { title: { display: true, text: 'Value' } } } } };
        const config2 = { type: 'line', data: data2, options: { scales: { x: { title: { display: true, text: 'Time' } }, y: { title: { display: true, text: 'Value' } } } } };
        const config3 = { type: 'line', data: data3, options: { scales: { x: { title: { display: true, text: 'Time' } }, y: { title: { display: true, text: 'Value' } } } } };

        const ctx1 = document.getElementById('lineChart1').getContext('2d');
        const ctx2 = document.getElementById('lineChart2').getContext('2d');
        const ctx3 = document.getElementById('lineChart3').getContext('2d');

        const lineChart1 = new Chart(ctx1, config1);
        const lineChart2 = new Chart(ctx2, config2);
        const lineChart3 = new Chart(ctx3, config3);

        function updateChart() {
            const currentTime = new Date().toLocaleTimeString();
            switch (currentGraph) {
                case 1:
                    data1.labels.push(currentTime);
                    data1.datasets[0].data.push(value1);
                    lineChart1.update();
                    break;
                case 2:
                    data2.labels.push(currentTime);
                    data2.datasets[0].data.push(value2);
                    lineChart2.update();
                    break;
                case 3:
                    data3.labels.push(currentTime);
                    data3.datasets[0].data.push(value3);
                    lineChart3.update();
                    break;
            }
        }

        intervalId = setInterval(updateChart, refreshInterval);

        function showGraph(graphNumber) {
            document.querySelectorAll('.chart-container').forEach((container, index) => {
                container.style.display = (index + 1 === graphNumber) ? 'block' : 'none';
            });
            currentGraph = graphNumber;
            updateDisplay();
        }
    </script>
</body>
</html>
