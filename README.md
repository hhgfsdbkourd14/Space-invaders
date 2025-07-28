# Space-invaders
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Space Invaders Mobile</title>
  <style>
    body {
      margin: 0;
      background-color: black;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      flex-direction: column;
      font-family: sans-serif;
    }

    .game-container {
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .grid {
      width: 240px;
      height: 240px;
      display: grid;
      grid-template-columns: repeat(12, 1fr);
      gap: 1px;
      background-color: #222;
    }

    .grid div {
      width: 20px;
      height: 20px;
      background-color: black;
    }

    .invader {
      background-color: limegreen !important;
    }

    .shooter {
      background-color: red !important;
    }

    .laser {
      background-color: yellow !important;
    }

    .boom {
      background-color: white !important;
    }

    .controls {
      display: flex;
      gap: 15px;
      margin-top: 20px;
    }

    .controls button {
      padding: 15px 20px;
      font-size: 24px;
      background-color: #444;
      color: white;
      border: none;
      border-radius: 10px;
    }
  </style>
</head>
<body>
  <div class="game-container">
    <div class="grid" id="grid"></div>
    <div class="controls">
      <button id="left">◀️</button>
      <button id="shoot">🔫</button>
      <button id="right">▶️</button>
    </div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const grid = document.getElementById('grid');
      const width = 12;
      const squares = [];

      for (let i = 0; i < width * width; i++) {
        const square = document.createElement('div');
        grid.appendChild(square);
        squares.push(square);
      }

      let currentShooterIndex = 132;
      squares[currentShooterIndex].classList.add('shooter');

      let invaders = [
        0,1,2,3,4,5,6,7,8,9,
        12,13,14,15,16,17,18,19,20,21,
        24,25,26,27,28,29,30,31,32,33
      ];

      function drawInvaders() {
        invaders.forEach(i => {
          if (!squares[i].classList.contains('boom')) {
            squares[i].classList.add('invader');
          }
        });
      }

      function removeInvaders() {
        invaders.forEach(i => squares[i].classList.remove('invader'));
      }

      drawInvaders();

      function moveShooter(dir) {
        squares[currentShooterIndex].classList.remove('shooter');
        if (dir === 'left' && currentShooterIndex % width !== 0) {
          currentShooterIndex--;
        }
        if (dir === 'right' && currentShooterIndex % width < width - 1) {
          currentShooterIndex++;
        }
        squares[currentShooterIndex].classList.add('shooter');
      }

      document.getElementById('left').addEventListener('click', () => moveShooter('left'));
      document.getElementById('right').addEventListener('click', () => moveShooter('right'));

      function shootLaser() {
        let laserIndex = currentShooterIndex;

        function moveLaser() {
          if (laserIndex < width) {
            clearInterval(laserInterval);
            return;
          }

          squares[laserIndex].classList.remove('laser');
          laserIndex -= width;

          if (squares[laserIndex].classList.contains('invader')) {
            squares[laserIndex].classList.remove('invader');
            squares[laserIndex].classList.add('boom');
            setTimeout(() => squares[laserIndex].classList.remove('boom'), 300);
            clearInterval(laserInterval);
            const hit = invaders.indexOf(laserIndex);
            invaders.splice(hit, 1);
            return;
          }

          squares[laserIndex].classList.add('laser');
        }

        const laserInterval = setInterval(moveLaser, 100);
      }

      document.getElementById('shoot').addEventListener('click', shootLaser);

      let direction = 1;
      function moveInvaders() {
        const leftEdge = invaders[0] % width === 0;
        const rightEdge = invaders[invaders.length - 1] % width === width - 1;

        removeInvaders();

        if (rightEdge && direction === 1) {
          for (let i = 0; i < invaders.length; i++) {
            invaders[i] += width + 1;
          }
          direction = -1;
        } else if (leftEdge && direction === -1) {
          for (let i = 0; i < invaders.length; i++) {
            invaders[i] += width - 1;
          }
          direction = 1;
        } else {
          for (let i = 0; i < invaders.length; i++) {
            invaders[i] += direction;
          }
        }

        drawInvaders();

        if (invaders.includes(currentShooterIndex)) {
          alert('👾 GAME OVER');
          clearInterval(invaderTimer);
        }

        if (invaders.length === 0) {
          alert('🎉 YOU WIN');
          clearInterval(invaderTimer);
        }
      }

      const invaderTimer = setInterval(moveInvaders, 600);
    });
  </script>
</body>
</html>
