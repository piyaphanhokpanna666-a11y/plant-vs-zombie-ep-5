document.addEventListener("DOMContentLoaded", () => {
  var zombies = [];
  var plantAnimation = 0;
  var plants = [];
  var bullets = [];
  var counter = 0;
  var score = 0;
  var aiEnabled = false;

  document.getElementById('ai-assist').addEventListener('click', () => {
    aiEnabled = !aiEnabled;
    document.getElementById('ai-assist').textContent = aiEnabled ? 'Turn AI Off' : 'Turn AI On';
  });

  document.getElementById('background').addEventListener('click', function(e) {
    var rect = this.getBoundingClientRect();
    var x = e.clientX - rect.left;
    var y = e.clientY - rect.top;
    var col = Math.floor((x - 265) / 80);
    var lane = Math.floor((y - 85) / 100);
    if (col >= 0 && col < 9 && lane >= 0 && lane < 5) {
      if (!plants.some(p => p.x == col && p.y == lane)) {
        plants.push({x: col, y: lane, health: 50});
        drawPlants();
      }
    }
  });

  function gameLoop() {
    counter++;
    if (counter % 5 == 0) {
      moveZombies();
      drawZombies();
    }
    if (aiEnabled && counter % 100 == 0) {
      aiHelp();
    }
  }
  setInterval(gameLoop, 30);
  setInterval(fireBullets, 3000);
  setInterval(() => {
    moveBullets();
    drawBullets();
    drawPlants();
  }, 10);
  setInterval(() => {
    if (plantAnimation < 7) {
      plantAnimation += 1;
    } else if (plantAnimation == 7) {
      plantAnimation = 0;
    }
  }, 120);
  setInterval(spawnZombie, 5000);

  function aiHelp() {
    var lanesWithZombies = new Set(zombies.map(z => z.y));
    var lanesWithPlants = new Set(plants.map(p => p.y));
    for (let lane of lanesWithZombies) {
      if (!lanesWithPlants.has(lane)) {
        var col = 0;
        while (plants.some(p => p.x == col && p.y == lane) && col < 8) {
          col++;
        }
        if (col < 9) {
          plants.push({x: col, y: lane, health: 50});
          drawPlants();
        }
      }
    }
  }

  function spawnZombie() {
    var type = Math.floor(Math.random() * 4) + 1;
    var y = Math.floor(Math.random() * 5);
    var x = 900 + Math.random() * 200;
    var health = 50 + type * 10;
    zombies.push({
      health: health,
      type: type,
      x: x,
      y: y,
      animation: 0,
      lastAttack: 0,
      attacking: false
    });
  }

  function playSound(path) {
    var audio = new Audio(path);
    audio.play();
  }

  function fireBullets() {
    var fired = false;
    for (var i = 0; i < plants.length; i++) {
      var plantY = plants[i].y;
      var hasZombie = zombies.some(z => z.y == plantY && z.x < 950 && z.health > 0);
      if (hasZombie) {
        bullets.push({
          x: plants[i].x * 80 + 300,
          y: plantY,
        });
        fired = true;
      }
    }
    if (fired) playSound("puff.mp3");
  }

  function moveZombies() {
    for (var i = 0; i < zombies.length; i++) {
      var zombie = zombies[i];
      if (zombie.health <= 0) continue;

      var plantInWay = plants.find(p => p.y == zombie.y && Math.abs(zombie.x - (p.x * 80 + 265)) < 20 && p.health > 0);
      if (plantInWay) {
        zombie.attacking = true;
        if (Date.now() - zombie.lastAttack > 1000) {
          plantInWay.health -= 10;
          zombie.lastAttack = Date.now();
          if (plantInWay.health <= 0) {
            plants = plants.filter(p => p != plantInWay);
            zombie.attacking = false;
          }
        }
      } else {
        zombie.attacking = false;
        zombie.x -= 1;
        if (zombie.x <= 150) {
          alert('Game Over! Zombie reached the house.');
          location.reload();
        }
      }

      zombie.animation = (zombie.animation + 1) % 5;
    }
  }

  function moveBullets() {
    if (bullets.length > 0) {
      for (var i = 0; i < bullets.length; i++) {
        if (bullets[i].x < 1000) {
          bullets[i].x += 1;
          for (var j = zombies.length - 1; j >= 0; j--) {
            if (
              bullets[i].x > zombies[j].x - 3 &&
              bullets[i].y == zombies[j].y &&
              zombies[j].health >= 10
            ) {
              playSound("puff.mp3");
              zombies[j].health -= 10;

              if (zombies[j].health <= 0) {
                playSound("die.mp3");
                score += 10;
                document.getElementById('score').innerText = 'Score: ' + score;
                var exp = document.createElement('div');
                exp.className = 'explosion';
                exp.innerHTML = '💥';
                exp.style.left = zombies[j].x + 'px';
                exp.style.top = (zombies[j].y * 100 + 20) + 'px';
                document.getElementById('background').appendChild(exp);
                setTimeout(() => exp.remove(), 500);
                zombies.splice(j, 1);
              }

              bullets.splice(i, 1);
              i--;
              break;
            }
          }
        } else {
          bullets.splice(i, 1);
          i--;
        }
      }
    }
  }

  function drawBullets() {
    var html = "";
    for (var i = 0; i < bullets.length; i++) {
      html += `<div class="bullet" style='top: ${
        bullets[i].y * 100 + 85
      }px; left: ${bullets[i].x}px;'></div>`;
    }
    document.getElementById("bullets").innerHTML = html;
  }

  function drawZombies() {
    var html = "";
    for (var i = 0; i < zombies.length; i++) {
      var zombie = zombies[i];
      if (zombie.health > 0 && zombie.x < 1024) {
        html +=
          "<div class='zombie" +
          zombie.type +
          "' style='top:" +
          (zombie.y * 100 + 20) +
          "px; left:" +
          zombie.x +
          "px; background-position-x: -" +
          zombie.animation * 128 +
          "px;'></div>";
      }
    }

    document.getElementById("zombies").innerHTML = html;
  }

  function drawPlants() {
    var html = "";
    for (var i = 0; i < plants.length; i++) {
      var plant = plants[i];
      if (plant.health > 0) {
        html += `<div class="plant1" style="top: ${
          plant.y * 100 + 85
        }px; left: ${plant.x * 80 + 265}px; background-position-x: -${
          plantAnimation * 60 - 3
        }px;"></div>`;
      }
    }

    document.getElementById("plants").innerHTML = html;
  }
  drawZombies();
  drawPlants();
});
