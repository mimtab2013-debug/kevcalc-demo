# kevcalc-demo
Scaffoldincalctool
<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>KevCalc SUVA PRO</title>

<style>
body {
    margin: 0;
    font-family: Arial, Helvetica, sans-serif;
    background: #0f172a;
    color: #e5e7eb;
}

header {
    background: #020617;
    padding: 12px;
    text-align: center;
    font-size: 20px;
    font-weight: bold;
    color: #38bdf8;
}

.container {
    display: flex;
    flex-direction: column;
    padding: 12px;
    gap: 12px;
}

.card {
    background: #020617;
    padding: 12px;
    border-radius: 10px;
}

label {
    display: block;
    margin-top: 8px;
    font-size: 14px;
}

input, select {
    width: 100%;
    padding: 8px;
    margin-top: 4px;
    border-radius: 6px;
    border: none;
    background: #0f172a;
    color: white;
}

button {
    margin-top: 12px;
    width: 100%;
    padding: 12px;
    border: none;
    border-radius: 8px;
    background: #38bdf8;
    color: black;
    font-size: 16px;
    font-weight: bold;
}

.result {
    font-size: 14px;
    line-height: 1.6;
}

canvas {
    width: 100%;
    height: 240px;
    background: #020617;
    border-radius: 10px;
}
</style>
</head>

<body>

<header>KevCalc SUVA PRO – Demo</header>

<div class="container">

<div class="card">
<h3>Gebäudedaten</h3>

<label>Breite (m)</label>
<input type="number" id="width" value="18">

<label>Länge (m)</label>
<input type="number" id="length" value="10">

<label>Traufhöhe (m)</label>
<input type="number" id="height" value="8">

<label>Firsthöhe (m)</label>
<input type="number" id="ridge" value="10">

<label>Dachform</label>
<select id="roof">
<option>Satteldach</option>
<option>Flachdach</option>
</select>

<button onclick="calculate()">Berechnen</button>
</div>

<div class="card result" id="output">
➡️ Bereit für Berechnung
</div>

<div class="card">
<h3>3D-Visualisierung (Demo)</h3>
<canvas id="canvas"></canvas>
</div>

</div>

<script>
function calculate() {
    const width = parseFloat(document.getElementById("width").value);
    const height = parseFloat(document.getElementById("height").value);
    const ridge = parseFloat(document.getElementById("ridge").value);

    // SUVA Ebenen: 0.5m Start + 2.0m Raster
    let levels = Math.ceil((Math.max(height, ridge) - 0.5) / 2);

    // KI Feldlogik: 2.57 bevorzugt
    let fields257 = Math.floor(width / 2.57);
    let rest = width - fields257 * 2.57;

    let fields307 = 0;
    if (rest > 1.2) {
        fields307 = 1;
    }

    // Material (vereinfachte Demo)
    let standards = (fields257 + fields307 + 1) * levels;
    let decks = (fields257 + fields307) * levels;
    let weight = standards * 17 + decks * 19;

    document.getElementById("output").innerHTML = `
    <b>SUVA Ebenen:</b> ${levels}<br>
    <b>Felder 2.57 m:</b> ${fields257}<br>
    <b>Felder 3.07 m (Ausnahme):</b> ${fields307}<br><br>

    <b>Material:</b><br>
    Stiele: ${standards}<br>
    Beläge: ${decks}<br><br>

    <b>Gesamtgewicht:</b> ~ ${weight} kg
    `;

    drawScaffold(levels);
}

function drawScaffold(levels) {
    const canvas = document.getElementById("canvas");
    const ctx = canvas.getContext("2d");
    canvas.width = canvas.offsetWidth;
    canvas.height = canvas.offsetHeight;

    ctx.clearRect(0,0,canvas.width,canvas.height);

    ctx.strokeStyle = "#38bdf8";
    ctx.lineWidth = 2;

    let baseY = canvas.height - 20;
    let levelHeight = (canvas.height - 40) / levels;

    for (let i = 0; i <= levels; i++) {
        ctx.beginPath();
        ctx.moveTo(20, baseY - i * levelHeight);
        ctx.lineTo(canvas.width - 20, baseY - i * levelHeight);
        ctx.stroke();
    }

    ctx.beginPath();
    ctx.moveTo(40, 20);
    ctx.lineTo(40, baseY);
    ctx.moveTo(canvas.width - 40, 20);
    ctx.lineTo(canvas.width - 40, baseY);
    ctx.stroke();
}
</script>

</body>
</html>
