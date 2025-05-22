<script setup>
import { onMounted, ref } from 'vue';
import * as THREE from 'three';
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';

const canvasRef = ref(null);

const cameraPositions = [
  { x: 6.33, y: 5, z: 9.01, rotY: 0.60 },
  { x: -1.87, y: 3.30, z: 1.05, rotY: 1.41 },
  { x: -1.03, y: 3.40, z: -3.17, rotY: 0.87 },
  { x: 0.35, y: 2.10, z: -8.73, rotY: -1.80 },
];
const currentIndex = ref(0);

const infoPanels = [
  {
    title: 'My room',
    text: 'So this is my virtual room. I have a lot of things here, including a computer, camera, skateboard and a guitar.',
    image: '',
    extra: '<ul><li>Feel free to explore!</li><li>Press Next and Back to toggle views and gain a little info about me</li></ul>'
  },
  {
    title: 'Cinematography',
    text: 'I’m into filmmaking and cinematography, where I get to explore storytelling through visuals. I’m also inspired by travel and the idea of vanlife, and I want to bring all of these passions together in my projects, blending music, visuals, and experiences into something unique.',
    image: '',
    extra: ''
  },
  {
    title: 'Digital creator',
    text: 'I’m Scripty2k, a digital creator from the Netherlands. I love to make stuff on the internet. I mainly like making stuff digitally. Like websites, music, programs and videography/cinematography.',
    image: '',
    extra: '<ul><li>Check out my <a href="https://scripty2k.com" target="_blank">website</a></li><li>Follow me on <a href="https://www.instagram.com/scripty2k/" target="_blank">Instagram</a></li></ul>'
  },
  {
    title: 'In my free time',
    text: 'I like to skateboard, play guitar and hang out with friends. I also love to travel and explore new places when I am older. I like nature a lot',
    image: '',
    extra: ''
  },
];

let camera, scene, renderer;
let targetPosition = { ...cameraPositions[0] };
let targetRotY = cameraPositions[0].rotY ?? 0;

const moveToIndex = (idx) => {
  if (idx >= 0 && idx < cameraPositions.length) {
    currentIndex.value = idx;
    targetPosition = { ...cameraPositions[idx] };
    targetRotY = cameraPositions[idx].rotY ?? 0;
  }
};

const nextPosition = () => {
  if (currentIndex.value < cameraPositions.length - 1) {
    moveToIndex(currentIndex.value + 1);
  } else {
    // Loop to first
    moveToIndex(0);
  }
};

const prevPosition = () => {
  if (currentIndex.value > 0) {
    moveToIndex(currentIndex.value - 1);
  } else {
    // Loop to last
    moveToIndex(cameraPositions.length - 1);
  }
};

onMounted(() => {
  const canvas = canvasRef.value;

  scene = new THREE.Scene();
  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  renderer = new THREE.WebGLRenderer({ canvas });

  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);

  // Add light
  const light = new THREE.DirectionalLight(0xffffff, 1);
  light.position.set(10, 10, 10);
  scene.add(light);

  // Load scene.gltf model
  const loader = new GLTFLoader();
  loader.load(
    '/src/assets/scene.gltf',
    (gltf) => {
      const object = gltf.scene;
      object.position.set(0, 0, 0);
      object.scale.set(1, 1, 1);
      scene.add(object);
    },
    undefined,
    (error) => {
      console.error('Error loading scene.gltf:', error);
      alert('Failed to load scene.gltf.');
    }
  );

  // Set initial camera position and rotation
  camera.position.set(targetPosition.x, targetPosition.y, targetPosition.z);
  camera.rotation.y = targetRotY;

  // Play background music
  const audio = new Audio('/src/assets/music.wav');
  audio.loop = true;
  audio.volume = 0.5;
  // Try to play after user interaction (for browser autoplay policy)
  const playAudio = () => {
    audio.play();
    window.removeEventListener('click', playAudio);
  };
  window.addEventListener('click', playAudio);

  // Animation loop
  const animate = () => {
    requestAnimationFrame(animate);

    // Smoothly interpolate camera position to target
    camera.position.x += (targetPosition.x - camera.position.x) * 0.1;
    camera.position.y += (targetPosition.y - camera.position.y) * 0.1;
    camera.position.z += (targetPosition.z - camera.position.z) * 0.1;

    // Smoothly interpolate camera rotation.y to targetRotY
    let delta = targetRotY - camera.rotation.y;
    // Keep delta in [-PI, PI] for shortest path
    if (delta > Math.PI) delta -= 2 * Math.PI;
    if (delta < -Math.PI) delta += 2 * Math.PI;
    camera.rotation.y += delta * 0.1;

    renderer.render(scene, camera);
  };

  animate();

  window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
    renderer.setPixelRatio(window.devicePixelRatio);
  });
});
</script>

<template>
  <div class="main-3d-full">
    <div class="logo"/>
    <canvas ref="canvasRef"></canvas>
    <div class="button-bar">
      <button @click="prevPosition">Back</button>
      <button @click="nextPosition">Next</button>
    </div>
    <div class="info-panel">
      <h2>{{ infoPanels[currentIndex].title }}</h2>
      <img :src="infoPanels[currentIndex].image" alt="" class="info-image" />
      <p>{{ infoPanels[currentIndex].text }}</p>
      <div v-html="infoPanels[currentIndex].extra"></div>
    </div>
  </div>
</template>

<style scoped>
.main-3d-full {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  margin: 0;
  padding: 0;
  background: #111;
  overflow: hidden;
  z-index: 1;
}
canvas {
  display: block;
  width: 100vw !important;
  height: 100vh !important;
  margin: 0;
  padding: 0;
  background: #111;
}

.button-bar {
  position: absolute;
  top: 50%;
  left: 0;
  width: 100vw;
  display: flex;
  justify-content: space-between;
  transform: translateY(-50%);
  pointer-events: none;
  z-index: 20;
}
.button-bar button {
  pointer-events: auto;
  padding: 10px 18px;
  font-size: 16px;
  cursor: pointer;
  margin: 0 10px;
}

.info-panel {
  position: absolute;
  top: 50%;
  left: 60vw;
  transform: translateY(-50%);
  width: 320px;
  max-height: 60vh;
  background: rgba(255,255,255,0.97);
  color: #222;
  border-radius: 12px;
  box-shadow: 0 4px 24px rgba(0,0,0,0.18);
  padding: 24px 20px 20px 20px;
  overflow-y: auto;
  z-index: 30;
  display: flex;
  flex-direction: column;
  gap: 12px;
}
.info-panel h2 {
  margin: 0 0 8px 0;
  font-size: 1.3em;
}
.info-panel .info-image {
  width: 100%;
  border-radius: 8px;
  margin-bottom: 10px;
  object-fit: cover;
  max-height: 140px;
}
.info-panel ul {
  margin: 0 0 0 18px;
  padding: 0;
}
</style>