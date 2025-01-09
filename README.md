<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ruleta de Roles</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
        }

        .wheel-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 20px;
            padding: 20px;
            background-color: white;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }

        #spinButton {
            padding: 10px 20px;
            font-size: 16px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: background-color 0.3s;
        }

        #spinButton:hover {
            background-color: #45a049;
        }

        #result {
            font-size: 18px;
            font-weight: bold;
            margin-top: 20px;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="wheel-container">
        <canvas id="roleWheel" width="400" height="400"></canvas>
        <button id="spinButton">Girar Ruleta</button>
        <div id="result"></div>
    </div>

    <script>
        const roles = [
            "Profesor Luis",
            "Profesora Ana",
            "María (Moderadora)",
            "Observador/a",
            "Profesor Carlos"
        ];

        const colors = ['#FF6B6B', '#4ECDC4', '#45B7D1', '#96CEB4', '#FFEEAD'];

        const canvas = document.getElementById('roleWheel');
        const ctx = canvas.getContext('2d');
        const resultDiv = document.getElementById('result');
        let currentRotation = 0;
        let isSpinning = false;

        function drawWheel() {
            const centerX = canvas.width / 2;
            const centerY = canvas.height / 2;
            const radius = Math.min(centerX, centerY) - 10;
            const anglePerSegment = (Math.PI * 2) / roles.length;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            roles.forEach((role, index) => {
                // Dibujar segmento
                ctx.beginPath();
                ctx.moveTo(centerX, centerY);
                ctx.arc(
                    centerX, 
                    centerY, 
                    radius,
                    index * anglePerSegment + currentRotation,
                    (index + 1) * anglePerSegment + currentRotation
                );
                ctx.closePath();
                ctx.fillStyle = colors[index];
                ctx.fill();
                ctx.stroke();

                // Agregar texto
                ctx.save();
                ctx.translate(centerX, centerY);
                ctx.rotate(index * anglePerSegment + anglePerSegment/2 + currentRotation);
                ctx.textAlign = "right";
                ctx.fillStyle = "#000";
                ctx.font = "14px Arial";
                ctx.fillText(role, radius - 20, 5);
                ctx.restore();
            });

            // Dibujar círculo central
            ctx.beginPath();
            ctx.arc(centerX, centerY, 10, 0, Math.PI * 2);
            ctx.fillStyle = '#333';
            ctx.fill();

            // Dibujar flecha
            ctx.beginPath();
            ctx.moveTo(centerX + 10, centerY);
            ctx.lineTo(centerX + 30, centerY - 10);
            ctx.lineTo(centerX + 30, centerY + 10);
            ctx.closePath();
            ctx.fillStyle = '#333';
            ctx.fill();
        }

        document.getElementById('spinButton').addEventListener('click', () => {
            if (!isSpinning) {
                isSpinning = true;
                resultDiv.textContent = '';
                const spins = 5 + Math.random() * 5;
                const duration = 3000;
                const startTime = Date.now();
                
                function animate() {
                    const elapsed = Date.now() - startTime;
                    const progress = elapsed / duration;
                    
                    if (progress < 1) {
                        currentRotation = (spins * Math.PI * 2) * 
                            (1 - Math.pow(1 - progress, 3));
                        drawWheel();
                        requestAnimationFrame(animate);
                    } else {
                        isSpinning = false;
                        // Calcular el rol seleccionado
                        const anglePerSegment = (Math.PI * 2) / roles.length;
                        const normalizedRotation = currentRotation % (Math.PI * 2);
                        const selectedIndex = Math.floor(
                            (2 * Math.PI - (normalizedRotation % (2 * Math.PI))) / anglePerSegment
                        ) % roles.length;
                        resultDiv.textContent = `Rol seleccionado: ${roles[selectedIndex]}`;
                    }
                }
                
                animate();
            }
        });

        drawWheel();
    </script>
</body>
</html>
