<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Trái Tim Thầy Lý</title>
    <style>
        body { margin: 0; overflow: hidden; background-color: #000; }
        canvas { display: block; }
    </style>
</head>
<body>
    <canvas id="heart"></canvas>
    <script>
        const canvas = document.getElementById('heart');
        const ctx = canvas.getContext('2d');
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        function heartShape(t) {
            // Công thức trái tim
            return {
                x: 16 * Math.pow(Math.sin(t), 3),
                y: -(13 * Math.cos(t) - 5 * Math.cos(2 * t) - 2 * Math.cos(3 * t) - Math.cos(4 * t))
            };
        }

        const points = [];
        for (let t = 0; t < Math.PI * 2; t += 0.01) {
            points.push(heartShape(t));
        }

        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            ctx.save();
            ctx.translate(canvas.width / 2, canvas.height / 2);
            ctx.scale(20, 20); // Phóng to trái tim

            ctx.beginPath();
            ctx.lineWidth = 0.1;
            ctx.strokeStyle = '#ff4d4d'; // Màu đỏ
            ctx.shadowBlur = 15;
            ctx.shadowColor = '#ff4d4d'; // Hiệu ứng tỏa sáng

            for (let i = 0; i < points.length; i++) {
                ctx.lineTo(points[i].x, points[i].y);
            }
            ctx.closePath();
            ctx.stroke();
            ctx.restore();
        }
        
        draw();
        window.onresize = () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
            draw();
        };
    </script>
</body>
</html>
