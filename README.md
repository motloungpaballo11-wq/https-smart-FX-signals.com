 
<!DOCTYPE html>
<html>
<head>
  <title>Chart Signal Scanner</title>
  <script src="https://unpkg.com/lightweight-charts/dist/lightweight-charts.standalone.production.js"></script>
</head>
<body>

<h2>📈 Chart Signal Scanner</h2>
<div id="chart" style="width: 800px; height: 400px;"></div>

<script>
// Create chart
const chart = LightweightCharts.createChart(document.getElementById('chart'), {
  width: 800,
  height: 400
});

// Add candlestick series
const candleSeries = chart.addCandlestickSeries();

// Sample data (you can replace later with real API data)
const data = [
  { time: '2024-01-01', open: 100, high: 110, low: 90, close: 105 },
  { time: '2024-01-02', open: 105, high: 115, low: 95, close: 110 },
  { time: '2024-01-03', open: 110, high: 120, low: 100, close: 108 },
  { time: '2024-01-04', open: 108, high: 118, low: 102, close: 115 },
  { time: '2024-01-05', open: 115, high: 125, low: 110, close: 120 }
];

candleSeries.setData(data);

// Moving Average function
function movingAverage(data, period) {
  let result = [];

  for (let i = 0; i < data.length; i++) {
    if (i < period - 1) continue;

    let sum = 0;
    for (let j = 0; j < period; j++) {
      sum += data[i - j].close;
    }

    result.push({
      time: data[i].time,
      value: sum / period
    });
  }

  return result;
}

// Add Moving Average line
const maSeries = chart.addLineSeries({ color: 'blue' });
const maData = movingAverage(data, 3);
maSeries.setData(maData);

// Generate Buy Signals (simple strategy: price crosses above MA)
let markers = [];

for (let i = 1; i < maData.length; i++) {
  let prevClose = data[i - 1].close;
  let currentClose = data[i].close;
  let prevMA = maData[i - 1]?.value;
  let currentMA = maData[i]?.value;

  if (prevClose < prevMA && currentClose > currentMA) {
    markers.push({
      time: data[i].time,
      position: 'belowBar',
      color: 'green',
      shape: 'arrowUp',
      text: 'Buy'
    });
  }
}

// Add markers to chart
candleSeries.setMarkers(markers);

</script>

</body>
</html>
