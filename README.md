<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Python Heart JS Port</title>
<style>
    body {
        margin: 0;
        background: black;
        overflow: hidden;
    }
</style>
</head>
<body>
<canvas id="canvas"></canvas>

<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");

canvas.width = 640;
canvas.height = 480;

const WIDTH = canvas.width;
const HEIGHT = canvas.height;
const CENTER_X = WIDTH / 2;
const CENTER_Y = HEIGHT / 2;
const IMAGE_ENLARGE = 11;
const HEART_COLOR = "#ff7600";

function heartFunction(t, shrinkRatio = IMAGE_ENLARGE) {
    let x = 16 * Math.pow(Math.sin(t), 3);
    let y = -(13 * Math.cos(t)
            - 5 * Math.cos(2 * t)
            - 2 * Math.cos(3 * t)
            - Math.cos(4 * t));

    x *= shrinkRatio;
    y *= shrinkRatio;

    x += CENTER_X;
    y += CENTER_Y;

    return { x, y };
}

function scatterInside(x, y, beta = 0.15) {
    let ratioX = -beta * Math.log(Math.random());
    let ratioY = -beta * Math.log(Math.random());

    let dx = ratioX * (x - CENTER_X);
    let dy = ratioY * (y - CENTER_Y);

    return { x: x - dx, y: y - dy };
}

function shrink(x, y, ratio) {
    let force = 1 / Math.pow(
        (Math.pow(x - CENTER_X, 2) + Math.pow(y - CENTER_Y, 2)),
        0.6
    );

    let dx = ratio * force * (x - CENTER_X);
    let dy = ratio * force * (y - CENTER_Y);

    return { x: x - dx, y: y - dy };
}

function curve(p) {
    return 2 * (2 * Math.sin(4 * p)) / (2 * Math.PI);
}

let points = [];
let edgePoints = [];
let centerPoints = [];

function build() {
    for (let i = 0; i < 2000; i++) {
        let t = Math.random() * Math.PI * 2;
        points.push(heartFunction(t));
    }

    for (let p of points) {
        for (let i = 0; i < 3; i++) {
            edgePoints.push(scatterInside(p.x, p.y, 0.05));
        }
    }

    for (let i = 0; i < 4000; i++) {
        let p = points[Math.floor(Math.random() * points.length)];
        centerPoints.push(scatterInside(p.x, p.y, 0.17));
    }
}

function calcPosition(x, y, ratio) {
    let force = 1 / Math.pow(
        (Math.pow(x - CENTER_X, 2) + Math.pow(y - CENTER_Y, 2)),
        0.52
    );

    let dx = ratio * force * (x - CENTER_X) + (Math.random() * 2 - 1);
    let dy = ratio * force * (y - CENTER_Y) + (Math.random() * 2 - 1);

    return { x: x - dx, y: y - dy };
}

let frame = 0;

function render() {
    ctx.clearRect(0, 0, WIDTH, HEIGHT);
    ctx.fillStyle = HEART_COLOR;

    let ratio = 10 * curve(frame / 10 * Math.PI);

    function drawGroup(group, sizeMin, sizeMax) {
        for (let p of group) {
            let pos = calcPosition(p.x, p.y, ratio);
            let size = Math.floor(Math.random() * (sizeMax - sizeMin + 1)) + sizeMin;
            ctx.fillRect(pos.x, pos.y, size, size);
        }
    }

    drawGroup(points, 1, 3);
    drawGroup(edgePoints, 1, 2);
    drawGroup(centerPoints, 1, 2);

    frame++;
    setTimeout(() => {
      requestAnimationFrame(render);
    }, 160);
}

build();
render();
</script>
</body>
</html>
