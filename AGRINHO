// p5.js sketch: Festejando do Campo à Cidade - Game Script

// Game canvas size
const CANVAS_WIDTH = 800;
const CANVAS_HEIGHT = 450;

// Player class - a cheerful character collecting festive items moving horizontally
class Player {
  constructor() {
    this.x = CANVAS_WIDTH / 8;
    this.y = CANVAS_HEIGHT - 70;
    this.size = 48;
    this.speed = 6;
    this.color = '#ff6f61';
    this.direction = 0; // -1 left, 1 right, 0 idle
  }

  update() {
    this.x += this.direction * this.speed;
    this.x = constrain(this.x, this.size / 2, CANVAS_WIDTH - this.size / 2);
  }

  draw() {
    push();
    translate(this.x, this.y);

    // Body
    fill(this.color);
    stroke('#fff');
    strokeWeight(2);
    ellipse(0, 0, this.size, this.size * 1.3);

    // Face
    fill('#fff');
    ellipse(0, -this.size * 0.15, this.size * 0.7, this.size * 0.7);

    // Eyes
    fill('#333');
    ellipse(-this.size * 0.15, -this.size * 0.2, this.size * 0.12, this.size * 0.12);
    ellipse(this.size * 0.15, -this.size * 0.2, this.size * 0.12, this.size * 0.12);

    // Smiling mouth
    noFill();
    stroke('#333');
    strokeWeight(3);
    arc(0, -this.size * 0.05, this.size * 0.4, this.size * 0.22, 0, PI);

    // Festive party hat (triangle with pompom)
    fill('#ff595e');
    noStroke();
    triangle(-this.size * 0.35, -this.size * 0.6, this.size * 0.35, -this.size * 0.6, 0, -this.size * 1.05);
    fill('#fff');
    ellipse(0, -this.size * 1.05, this.size * 0.15);
    pop();
  }
}

// Item class - festive collectibles: balloon, party hat, music note
class Item {
  constructor(x, y, type, envRatio) {
    this.x = x;
    this.y = y;
    this.type = type;
    this.size = 28;
    this.speed = 2 + envRatio * 2; // Goes faster toward city
    this.color = this.assignColor();
  }
  assignColor() {
    switch (this.type) {
      case 'balloon': return '#ff595e';
      case 'hat': return '#1982c4';
      case 'note': return '#ffca3a';
      default: return '#6a994e';
    }
  }
  update() {
    this.y += this.speed * 0.7;
    if (this.y > CANVAS_HEIGHT + this.size) {
      this.respawn();
    }
  }
  respawn() {
    this.x = random(this.size / 2, CANVAS_WIDTH - this.size / 2);
    this.y = random(-CANVAS_HEIGHT, -this.size);
  }
  draw() {
    push();
    translate(this.x, this.y);
    noStroke();
    fill(this.color);
    switch (this.type) {
      case 'balloon':
        ellipse(0, 0, this.size * 0.9, this.size * 1.2);
        stroke('#333');
        line(0, this.size * 0.6, 0, this.size * 1);
        break;
      case 'hat':
        triangle(-this.size * 0.5, 0, this.size * 0.5, 0, 0, -this.size);
        break;
      case 'note':
        ellipse(-this.size * 0.1, 0, this.size * 0.4, this.size * 0.6);
        rect(this.size * 0.1, -this.size * 0.6, this.size * 0.1, this.size * 1);
        break;
    }
    pop();
  }
}

// Colors for environments
const countrysideColors = {
  sky: '#8AD9FF',
  grass: '#529a45',
  hills: '#87c372',
  sun: '#FFD93D',
};

const cityColors = {
  sky: '#2e3a59',
  buildings: '#4b5576',
  windows: '#ffc542',
  street: '#3a3a3a',
  streetLine: '#fff',
};

// Game variables
let player;
let items = [];
let score = 0;
let lastItemSpawn = 0;
const itemSpawnInterval = 1500; // spawn every 1.5 seconds

function setup() {
  createCanvas(CANVAS_WIDTH, CANVAS_HEIGHT);
  player = new Player();
  score = 0;
  textFont('Poppins, sans-serif');
  textAlign(CENTER, CENTER);
}

function draw() {
  // Environment ratio from 0 (countryside left) to 1 (city right)
  const envRatio = constrain(map(player.x, 0, CANVAS_WIDTH, 0, 1), 0, 1);

  // Draw backgrounds: countryside base with city overlay fade
  drawCountryside(envRatio * 0.8);
  push();
  drawingContext.globalAlpha = envRatio;
  drawCity(envRatio);
  pop();

  // Update and draw items
  for (let i = items.length - 1; i >= 0; i--) {
    let it = items[i];
    it.update();
    it.draw();

    let d = dist(player.x, player.y, it.x, it.y);
    if (d < (player.size * 0.6 + it.size) / 2) {
      score++;
      items.splice(i, 1);
      spawnNewItem(envRatio);
    }
  }

  // Spawn items over time
  if (millis() - lastItemSpawn > itemSpawnInterval) {
    spawnNewItem(envRatio);
    lastItemSpawn = millis();
  }

  // Player update/draw
  player.update();
  player.draw();

  // HUD - Score display
  drawScore();
}

// Draw countryside background with gradient transitions
function drawCountryside(envRatio) {
  const cSky = lerpColor(color(countrysideColors.sky), color(cityColors.sky), envRatio);
  background(cSky);

  noStroke();
  fill(lerpColor(color(countrysideColors.sun), color(0, 0), envRatio));
  circle(CANVAS_WIDTH * 0.85, CANVAS_HEIGHT * 0.3, 80);

  const hillColor = lerpColor(color(countrysideColors.hills), color(cityColors.buildings), envRatio);
  const grassColor = lerpColor(color(countrysideColors.grass), color(cityColors.street), envRatio);

  fill(hillColor);
  ellipse(CANVAS_WIDTH * 0.3, CANVAS_HEIGHT * 0.8, 320, 150);
  ellipse(CANVAS_WIDTH * 0.65, CANVAS_HEIGHT * 0.85, 380, 200);

  fill(grassColor);
  rect(0, CANVAS_HEIGHT * 0.88, CANVAS_WIDTH, CANVAS_HEIGHT * 0.12);
}

// Draw city background fade in
function drawCity(envRatio) {
  fill(cityColors.buildings);
  const baseY = CANVAS_HEIGHT * 0.88;

  for (let i = 0; i < 8; i++) {
    const bX = i * 100 + 20;
    const bHeight = lerp(80, 220, noise(i + frameCount * 0.01));
    rect(bX, baseY - bHeight, 60, bHeight);

    fill(cityColors.windows);
    for (let y = baseY - bHeight + 10; y < baseY - 20; y += 30) {
      for (let x = bX + 8; x < bX + 52; x += 20) {
        if (random() > 0.5) rect(x, y, 12, 18, 3);
      }
    }
    fill(cityColors.buildings);
  }

  fill(cityColors.street);
  rect(0, baseY, CANVAS_WIDTH, CANVAS_HEIGHT - baseY);

  stroke(cityColors.streetLine);
  strokeWeight(3);
  for (let x = 0; x < CANVAS_WIDTH; x += 60) {
    line(x, baseY + CANVAS_HEIGHT * 0.07, x + 30, baseY + CANVAS_HEIGHT * 0.07);
  }
  noStroke();
}

// Spawn a new festive item, type chosen by environment progress ratio
function spawnNewItem(envRatio) {
  let type = 'balloon';
  if (envRatio < 0.3) type = 'balloon';
  else if (envRatio < 0.7) type = random() < 0.5 ? 'hat' : 'balloon';
  else type = random() < 0.5 ? 'note' : 'hat';
  const x = random(30, CANVAS_WIDTH - 30);
  const y = random(-CANVAS_HEIGHT, -30);

  items.push(new Item(x, y, type, envRatio));
}

// Draw score UI on top center
function drawScore() {
  push();
  fill('#333d47EE');
  rectMode(CENTER);
  rect(CANVAS_WIDTH / 2, 28, 150, 42, 15);

  fill('#f94f6d'); // festive pink
  noStroke();
  textSize(24);
  textStyle(BOLD);
  text(`Pontos: ${score}`, CANVAS_WIDTH / 2, 28);
  pop();
}

// Keyboard controls
function keyPressed() {
  if (keyCode === LEFT_ARROW) player.direction = -1;
  if (keyCode === RIGHT_ARROW) player.direction = 1;
}
function keyReleased() {
  if ((keyCode === LEFT_ARROW && player.direction === -1) || (keyCode === RIGHT_ARROW && player.direction === 1)) {
    player.direction = 0;
  }
}

// Touch controls for mobile devices (left/right half screen to move left/right)
function touchStarted() {
  if (touchX < width / 2) player.direction = -1;
  else player.direction = 1;
  return false; // prevent default
}
function touchEnded() {
  player.direction = 0;
  return false;
}
