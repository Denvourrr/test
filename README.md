<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Slice Pie Animation</title>

<style>
    body {
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        margin: 0;
        background: #000;
        color: #fff;
        font-family: Arial, sans-serif;
    }

    .container {
        text-align: center;
        width: 100%;
        max-width: 400px;
        padding: 20px;
        box-sizing: border-box;
    }

    canvas {
        width: 100%;
        height: auto;
    }

    .label {
        margin-top: 18px;
        font-size: 20px;
        font-weight: 600;
    }
</style>
</head>
<body>

<div class="container">
    <canvas id="pie" width="350" height="350"></canvas>
    <div class="label" id="label"></div>
</div>

<script>
const canvas = document.getElementById("pie");
const ctx = canvas.getContext("2d");

const slices = [
    { label: "Processor", value: 30, color: "#ff3d3d" },
    { label: "GPU", value: 20, color: "#00eaff" },
    { label: "RAM", value: 25, color: "#8eff00" },
    { label: "Storage", value: 25, color: "#ffae00" }
];

let total = slices.reduce((a, b) => a + b.value, 0);

let currentSlice = 0;
let currentAngle = 0;

function animateSlice() {
    if (currentSlice >= slices.length) return;

    let slice = slices[currentSlice];
    let targetAngle = (slice.value / total) * (2 * Math.PI);
    let speed = 0.018; // Smooth animation
    
    let draw = () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        // Already drawn slices
        let start = 0;
        for (let i = 0; i < currentSlice; i++) {
            drawFullSlice(slices[i], start);
            start += (slices[i].value / total) * (2 * Math.PI);
        }

        // Current slice animation
        ctx.beginPath();
        ctx.moveTo(canvas.width/2, canvas.height/2);
        ctx.fillStyle = slice.color;

        ctx.arc(
            canvas.width/2,
            canvas.height/2,
            150,
            start,
            start + currentAngle
        );
        ctx.fill();

        document.getElementById("label").innerText =
            `${slice.label} - ${Math.round((currentAngle / targetAngle) * slice.value)}%`;

        if (currentAngle < targetAngle) {
            currentAngle += speed;
            requestAnimationFrame(draw);
        } else {
            currentAngle = 0;
            currentSlice++;
            document.getElementById("label").innerText =
                `${slice.label} - ${slice.value}%`;

            setTimeout(animateSlice, 600);
        }
    };

    draw();
}

function drawFullSlice(slice, startAngle) {
    ctx.beginPath();
    ctx.moveTo(canvas.width/2, canvas.height/2);
    ctx.fillStyle = slice.color;

    let endAngle = startAngle + (slice.value / total) * (2 * Math.PI);
    ctx.arc(canvas.width/2, canvas.height/2, 150, startAngle, endAngle);
    ctx.fill();
}

animateSlice();
</script>

</body>
</html>

