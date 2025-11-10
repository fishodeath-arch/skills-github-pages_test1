<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Motion Emoji</title>
<style>
  body {
    margin: 0;
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    background: #222;
    overflow: hidden;
    touch-action: none;
    color: white;
    font-family: sans-serif;
    text-align: center;
  }

  #emoji {
    font-size: 15vw;
    transition: transform 0.05s ease-out;
    display: inline-block;
  }

  #startBtn {
    padding: 15px 25px;
    font-size: 18px;
    cursor: pointer;
    border-radius: 10px;
    border: none;
    background: #ff9800;
    color: white;
  }
</style>
</head>
<body>
<div id="emoji">😎</div>
<button id="startBtn">Tap to Enable Motion</button>

<script>
const emoji = document.getElementById('emoji');
const startBtn = document.getElementById('startBtn');

let lastX = 0, lastY = 0, lastZ = 0;

// Function to handle motion
function handleMotion(event) {
  const acc = event.accelerationIncludingGravity || {x:0, y:0, z:0};

  // Tilt mapping
  const tiltX = acc.x / 10;
  const tiltY = acc.y / 10;

  // Shake detection
  const deltaX = acc.x - lastX;
  const deltaY = acc.y - lastY;
  const deltaZ = acc.z - lastZ;
  const shake = Math.min(Math.sqrt(deltaX**2 + deltaY**2 + deltaZ**2), 10);

  lastX = acc.x;
  lastY = acc.y;
  lastZ = acc.z;

  // Update emoji transform
  const scaleX = 1 + tiltX - shake*0.1;
  const scaleY = 1 - tiltX + shake*0.1;
  const rotate = tiltX * 20;

  emoji.style.transform = `scale(${scaleX}, ${scaleY}) rotate(${rotate}deg)`;
}

// iOS 13+ permission request
function enableMotion() {
  if (typeof DeviceMotionEvent !== 'undefined' && typeof DeviceMotionEvent.requestPermission === 'function') {
    DeviceMotionEvent.requestPermission()
      .then(response => {
        if (response === 'granted') {
          window.addEventListener('devicemotion', handleMotion);
          startBtn.style.display = 'none';
        } else {
          alert('Motion permission denied');
        }
      })
      .catch(console.error);
  } else {
    // Non-iOS devices
    window.addEventListener('devicemotion', handleMotion);
    startBtn.style.display = 'none';
  }
}

// Wait for user tap
startBtn.addEventListener('click', enableMotion);
</script>
</body>
</html>
