<title>Dumb Stocks</title>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
    /* Styling remains unchanged */
body {
font-family: 'Arial', sans-serif;
text-align: center;
@@ -251,21 +250,15 @@
updateDisplay();
alert(`Deposited $${amount}.`);
} else {
        alert("Please enter a valid positive deposit amount.");
        alert("Invalid deposit amount.");
}
}

function withdraw() {
      const amount = parseInt(document.getElementById('depositAmount').value);
      if (!isNaN(amount) && amount > 0) {
        if (amount <= currency) {
          currency += value; // Adjust based on the graph
          updateDisplay();
          alert(`Withdrew $${value}.`);
        }
      } else {
        alert("Input must be greater than zero!");
      }
      const payout = value;
      currency += payout;
      updateDisplay();
      alert(`Withdrew $${payout}.`);
}

const data = {
@@ -295,4 +288,21 @@
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

    intervalId = setInterval(updateChart, refreshInterval);
  </script>
</body>
</html>
