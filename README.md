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

    button:hover
