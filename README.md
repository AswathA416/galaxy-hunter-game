# galaxy-hunter-game
Galaxy Hunter

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Galaxy Skeleton</title>
<style>
* { box-sizing: border-box; }
body {
  margin: 0;
  overflow: hidden;
  background: #050014;
  font-family: Arial, sans-serif;
  color: white;
}
canvas { display: block; }
#hud {
  position: fixed;
  top: 15px;
  left: 15px;
  z-index: 2;
  background: #10072bcc;
  padding: 15px;
  border: 1px solid #874dff;
  border-radius: 12px;
  line-height: 1.8;
}
#help {
  position: fixed;
  bottom: 15px;
  left: 50%;
  transform: translateX(-50%);
  background: #10072bcc;
  padding: 12px 18px;
  border-radius: 10px;
  text-align: center;
  width: max-content;
  max-width: 95%;
  font-size: 13px;
}
#crosshair {
  position: fixed;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
  z-index: 2;
  font-size: 25px;
  pointer-events: none;
  color: #d9b7ff;
}
#start {
  position: fixed;
  inset: 0;
  z-index: 5;
  background: #080019ee;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  padding: 20px;
}
h1 { color: #c59aff; font-size: 42px; }
button {
  padding: 15px 35px;
  background: #873cff;
  color: white;
  border: none;
  border-radius: 10px;
  font-size: 18px;
  cursor: pointer;
}
</style>
</head>
<body>

<div id="hud">
  🌌 GALAXY SKELETON<br>
  ❤️ Health: <span id="health">100</span><br>
  ⭐ Score: <span id="score">0</span><br>
  👾 Enemies: <span id="remaining">5</span>
</div>

<div id="crosshair">+</div>

<div id="help">
  WASD: Move | Mouse: Look | Click: Shoot | SPACE: Jump
</div>

<div id="start">
  <h1>🌌 GALAXY SKELETON</h1>
  <p>Survive the galaxy arena and defeat the enemies!</p>
  <p>WASD to move • Mouse to aim • Click to shoot</p>
  <button id="play">PLAY GAME</button>
</div>

<script type="module">
import * as THREE from
'https://cdn.jsdelivr.net/npm/three@0.160.0/build/three.module.js';

// SCENE
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x080019);
scene.fog = new THREE.Fog(0x080019, 25, 100);

const camera = new THREE.PerspectiveCamera(
  70, window.innerWidth/window.innerHeight, 0.1, 200
);

const renderer = new THREE.WebGLRenderer({antialias:true});
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.shadowMap.enabled = true;
document.body.appendChild(renderer.domElement);

// LIGHTING
scene.add(new THREE.HemisphereLight(0x9999ff, 0x18002c, 2));

const light = new THREE.DirectionalLight(0xffffff, 2);
light.position.set(10,20,10);
scene.add(light);

const purpleLight = new THREE.PointLight(0x8b28ff, 100, 35);
purpleLight.position.set(0,5,0);
scene.add(purpleLight);

// MATERIALS
const galaxy = new THREE.MeshStandardMaterial({
  color: 0x633cff,
  emissive: 0x321080,
  emissiveIntensity: 0.8,
  roughness: 0.4
});

const bone = new THREE.MeshStandardMaterial({
  color: 0xd9d5ff,
  roughness: 0.5
});

const dark = new THREE.MeshStandardMaterial({
  color: 0x100c29,
  roughness: 0.7
});

const glow = new THREE.MeshBasicMaterial({
  color: 0xc45aff
});

// GROUND
const ground = new THREE.Mesh(
  new THREE.PlaneGeometry(120,120),
  new THREE.MeshStandardMaterial({
    color: 0x10072b,
    roughness: 0.9
  })
);
ground.rotation.x = -Math.PI/2;
ground.position.y = -0.1;
scene.add(ground);

const grid = new THREE.GridHelper(120,60,0x7435bb,0x29124f);
grid.position.y = 0;
scene.add(grid);

// STARS
const starGeometry = new THREE.BufferGeometry();
const starPositions = [];

for(let i=0;i<900;i++){
  starPositions.push(
    (Math.random()-0.5)*100,
    Math.random()*35+3,
    (Math.random()-0.5)*100
  );
}

starGeometry.setAttribute(
  'position',
  new THREE.Float32BufferAttribute(starPositions,3)
);

const stars = new THREE.Points(
  starGeometry,
  new THREE.PointsMaterial({
    color: 0xb99aff,
    size: 0.16
  })
);
scene.add(stars);

// BUILD PLAYER
const player = new THREE.Group();
scene.add(player);

function sphere(parent,x,y,z,sx,sy,sz,material){
  const mesh = new THREE.Mesh(
    new THREE.SphereGeometry(1,16,12),
    material
  );
  mesh.position.set(x,y,z);
  mesh.scale.set(sx,sy,sz);
  parent.add(mesh);
  return mesh;
}

function cylinder(parent,a,b,r1,r2,material){
  const start = new THREE.Vector3(...a);
  const end = new THREE.Vector3(...b);
  const direction = new THREE.Vector3().subVectors(end,start);
  const mesh = new THREE.Mesh(
    new THREE.CylinderGeometry(r2,r1,direction.length(),10),
    material
  );
  mesh.position.copy(start).add(end).multiplyScalar(0.5);
  mesh.quaternion.setFromUnitVectors(
    new THREE.Vector3(0,1,0),
    direction.normalize()
  );
  parent.add(mesh);
  return mesh;
}

// Hood
sphere(player,0,2.05,0,0.57,0.65,0.5,dark);

// Skull
sphere(player,0,2.15,-0.28,0.40,0.44,0.35,bone);

// Eye sockets
sphere(player,-0.17,2.19,-0.59,0.12,0.15,0.07,dark);
sphere(player,0.17,2.19,-0.59,0.12,0.15,0.07,dark);

// Glowing eyes
sphere(player,-0.17,2.19,-0.65,0.055,0.07,0.035,glow);
sphere(player,0.17,2.19,-0.65,0.055,0.07,0.035,glow);

// Nose
sphere(player,0,2.02,-0.62,0.06,0.09,0.04,dark);

// Body
cylinder(player,[0,1.3,0],[0,1.85,0],0.29,0.22,galaxy);

// Ribs
for(let i=0;i<4;i++){
  const y=1.45+i*0.12;
  const rib = new THREE.Mesh(
    new THREE.TorusGeometry(0.25,0.035,8,16,Math.PI),
    bone
  );
  rib.position.set(0,y,-0.04);
  rib.rotation.z=Math.PI;
  player.add(rib);
}

// Arms
cylinder(player,[-0.25,1.7,0],[ -0.52,1.3,0],0.10,0.07,bone);
cylinder(player,[-0.52,1.3,0],[-0.48,0.95,0],0.07,0.06,galaxy);
cylinder(player,[0.25,1.7,0],[0.52,1.3,0],0.10,0.07,bone);
cylinder(player,[0.52,1.3,0],[0.48,0.95,0],0.07,0.06,galaxy);

// Legs
cylinder(player,[-0.15,1.25,0],[-0.18,0.65,0],0.13,0.09,bone);
cylinder(player,[0.15,1.25,0],[0.18,0.65,0],0.13,0.09,bone);
cylinder(player,[-0.18,0.65,0],[-0.18,0.15,0],0.09,0.07,galaxy);
cylinder(player,[0.18,0.65,0],[0.18,0.15,0],0.09,0.07,galaxy);

// Boots
sphere(player,-0.18,0.12,-0.08,0.15,0.12,0.23,dark);
sphere(player,0.18,0.12,-0.08,0.15,0.12,0.23,dark);

// GUN
const gun = new THREE.Group();
player.add(gun);
gun.position.set(0.5,1.05,-0.2);

const barrel = new THREE.Mesh(
  new THREE.BoxGeometry(0.13,0.13,0.75),
  dark
);
barrel.position.z=-0.35;
gun.add(barrel);

const gunBody = new THREE.Mesh(
  new THREE.BoxGeometry(0.22,0.22,0.35),
  galaxy
);
gun.add(gunBody);

const gunGlow = new THREE.Mesh(
  new THREE.BoxGeometry(0.05,0.05,0.3),
  glow
);
gunGlow.position.set(0,0.12,-0.25);
gun.add(gunGlow);

// ARENA WALLS
function wall(x,z,sx,sz){
  const mesh=new THREE.Mesh(
    new THREE.BoxGeometry(sx,3,sz),
    new THREE.MeshStandardMaterial({
      color:0x24104a,
      emissive:0x301060
    })
  );
  mesh.position.set(x,1.5,z);
  scene.add(mesh);
}

wall(0,-30,60,2);
wall(0,30,60,2);
wall(-30,0,2,60);
wall(30,0,2,60);

// ENEMIES
const enemies=[];
const enemyMaterial=new THREE.MeshStandardMaterial({
  color:0x6c145f,
  emissive:0x41004f,
  emissiveIntensity:1
});

function makeEnemy(x,z){
  const enemy=new THREE.Group();

  sphere(enemy,0,1.5,0,0.38,0.48,0.35,enemyMaterial);
  sphere(enemy,0,2.05,0,0.32,0.32,0.3,dark);
  sphere(enemy,-0.12,2.08,-0.26,0.06,0.07,0.04,glow);
  sphere(enemy,0.12,2.08,-0.26,0.06,0.07,0.04,glow);

  cylinder(enemy,[-0.18,1.25,0],[-0.2,0.5,0],0.12,0.08,enemyMaterial);
  cylinder(enemy,[0.18,1.25,0],[0.2,0.5,0],0.12,0.08,enemyMaterial);

  enemy.position.set(x,0,z);
  enemy.userData.health=3;
  scene.add(enemy);
  enemies.push(enemy);
}

for(let i=0;i<5;i++){
  makeEnemy(
    (Math.random()-0.5)*35,
    (Math.random()-0.5)*35
  );
}

// GAME STATE
const keys={};
let health=100;
let score=0;
let yaw=0;
let pitch=0.1;
let verticalSpeed=0;
let grounded=true;
let started=false;
let gameOver=false;

const raycaster=new THREE.Raycaster();

const healthText=document.getElementById('health');
const scoreText=document.getElementById('score');
const remainingText=document.getElementById('remaining');

document.addEventListener('keydown',e=>{
  keys[e.code]=true;
});
document.addEventListener('keyup',e=>{
  keys[e.code]=false;
});

document.addEventListener('mousemove',e=>{
  if(document.pointerLockElement===renderer.domElement){
    yaw-=e.movementX*0.002;
    pitch-=e.movementY*0.002;
    pitch=THREE.MathUtils.clamp(pitch,-0.4,0.8);
  }
});

// SHOOTING
function shoot(){
  if(!started || gameOver) return;

  raycaster.setFromCamera(
    new THREE.Vector2(0,0),
    camera
  );

  const targets=[];
  enemies.forEach(e=>{
    e.traverse(child=>{
      if(child.isMesh) targets.push(child);
    });
  });

  const hits=raycaster.intersectObjects(targets,false);

  if(hits.length>0){
    let target=hits[0].object;
    let enemy=target;
    while(enemy && !enemies.includes(enemy)){
      enemy=enemy.parent;
    }

    if(enemy){
      enemy.userData.health--;
      if(enemy.userData.health<=0){
        scene.remove(enemy);
        enemies.splice(enemies.indexOf(enemy),1);
        score+=10;
        scoreText.textContent=score;
        remainingText.textContent=enemies.length;
      }
    }
  }
}

document.addEventListener('mousedown',e=>{
  if(e.button===0 && started && !gameOver){
    shoot();
  }
});

// START GAME
document.getElementById('play').addEventListener('click',()=>{
  started=true;
  document.getElementById('start').style.display='none';
  renderer.domElement.requestPointerLock();
});

// CAMERA
function updateCamera(){
  const distance=6;
  const target=new THREE.Vector3(
    player.position.x+Math.sin(yaw)*distance,
    player.position.y+3.2+pitch*2,
    player.position.z+Math.cos(yaw)*distance
  );

  camera.position.lerp(target,0.15);

  const look=new THREE.Vector3(
    player.position.x,
    player.position.y+1.5,
    player.position.z
  );
  camera.lookAt(look);
}

// MAIN LOOP
const clock=new THREE.Clock();
let lastDamage=0;

function animate(){
  requestAnimationFrame(animate);
  const dt=Math.min(clock.getDelta(),0.05);

  if(started && !gameOver){

    // MOVEMENT
    let forward=0;
    let sideways=0;

    if(keys['KeyW']) forward+=1;
    if(keys['KeyS']) forward-=1;
    if(keys['KeyA']) sideways-=1;
    if(keys['KeyD']) sideways+=1;

    const moving=forward!==0 || sideways!==0;

    if(moving){
      const length=Math.hypot(forward,sideways);
      forward/=length;
      sideways/=length;

      player.position.x+=(
        -Math.sin(yaw)*forward+
        Math.cos(yaw)*sideways
      )*8*dt;

      player.position.z+=(
        -Math.cos(yaw)*forward-
        Math.sin(yaw)*sideways
      )*8*dt;

      player.rotation.y=yaw;
    }

    // KEEP PLAYER IN ARENA
    player.position.x=THREE.MathUtils.clamp(
      player.position.x,-27,27
    );
    player.position.z=THREE.MathUtils.clamp(
      player.position.z,-27,27
    );

    // JUMP
    if(keys['Space'] && grounded){
      verticalSpeed=8;
      grounded=false;
    }

    player.position.y+=verticalSpeed*dt;
    verticalSpeed-=20*dt;

    if(player.position.y<=0){
      player.position.y=0;
      verticalSpeed=0;
      grounded=true;
    }

    // ENEMY AI
    enemies.forEach(enemy=>{
      const dx=player.position.x-enemy.position.x;
      const dz=player.position.z-enemy.position.z;
      const distance=Math.hypot(dx,dz);

      if(distance>1.5){
        enemy.position.x+=(dx/distance)*2.1*dt;
        enemy.position.z+=(dz/distance)*2.1*dt;
        enemy.rotation.y=Math.atan2(-dx,-dz);
      } else if(Date.now()-lastDamage>1000){
        health-=10;
        lastDamage=Date.now();
        healthText.textContent=health;
      }
    });

    if(health<=0){
      gameOver=true;
      document.exitPointerLock();
      document.getElementById('start').style.display='flex';
      document.querySelector('#start h1').textContent='GAME OVER';
      document.querySelector('#start p').textContent=
        'Your score: '+score;
      document.getElementById('play').textContent='PLAY AGAIN';
      document.getElementById('play').onclick=()=>location.reload();
    }

    if(enemies.length===0){
      gameOver=true;
      document.exitPointerLock();
      document.getElementById('start').style.display='flex';
      document.querySelector('#start h1').textContent='YOU WIN!';
      document.querySelector('#start p').textContent=
        'You defeated every enemy! Score: '+score;
      document.getElementById('play').textContent='PLAY AGAIN';
      document.getElementById('play').onclick=()=>location.reload();
    }

    updateCamera();
    stars.rotation.y+=0.00015;
  }

  renderer.render(scene,camera);
}

animate();

// RESIZE
window.addEventListener('resize',()=>{
  camera.aspect=window.innerWidth/window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth,window.innerHeight);
});
</script>
</body>
</html>
