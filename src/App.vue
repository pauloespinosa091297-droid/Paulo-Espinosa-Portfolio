<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue';
import { NeatGradient } from "@firecms/neat";

import Navbar from './components/Navbar.vue';
import About from './components/About.vue';
import Projects from './components/Projects.vue';
import Tools from './components/Tools.vue';
import Contact from './components/Contact.vue';
import Footer from './components/Footer.vue';

const gradientRef = ref(null);
let gradientInstance = null;

// Flashlight Mouse Coordinate Tracking States
const mouseX = ref(0);
const mouseY = ref(0);

const handleScroll = () => {
  if (gradientInstance) {
    gradientInstance.yOffset = window.scrollY;
  }
};

const handleFlashlightMove = (event) => {
  mouseX.value = event.clientX;
  mouseY.value = event.clientY;
};

onMounted(() => {
  const config = {
    colors: [
      { color: '#000000', enabled: true },
      { color: '#001129', enabled: true },
      { color: '#0F0025', enabled: true },
      { color: '#14080A', enabled: true },
      { color: '#001129', enabled: true },
    ],
    speed: 2,
    horizontalPressure: 4,
    verticalPressure: 4,
    waveFrequencyX: 3,
    waveFrequencyY: 2,
    waveAmplitude: 1,
    shadows: 2,
    highlights: 2,
    colorBrightness: 1,
    colorSaturation: -1,
    wireframe: false,
    colorBlending: 7,
    backgroundColor: '#010101',
    backgroundAlpha: 1,
    grainScale: 2,
    grainSparsity: 0,
    grainIntensity: 0,
    grainSpeed: 1,
    resolution: 0.75,
    yOffset: 600,
    yOffsetWaveMultiplier: 2.2,
    yOffsetColorMultiplier: 2.5,
    yOffsetFlowMultiplier: 2.8,
    flowDistortionA: 0.4,
    flowDistortionB: 3,
    flowScale: 3.3,
    flowEase: 0.53,
    flowEnabled: false,
    enableProceduralTexture: false,
    transparentTextureVoid: false,
    textureVoidLikelihood: 0.06,
    textureVoidWidthMin: 10,
    textureVoidWidthMax: 500,
    textureBandDensity: 0.8,
    textureColorBlending: 0.06,
    textureSeed: 333,
    textureEase: 0.68,
    proceduralBackgroundColor: '#003FFF',
    textureShapeTriangles: 20,
    textureShapeCircles: 15,
    textureShapeBars: 15,
    textureShapeSquiggles: 10,
    domainWarpEnabled: false,
    domainWarpIntensity: 0,
    domainWarpScale: 3,
    vignetteIntensity: 0,
    vignetteRadius: 0.8,
    fresnelEnabled: false,
    fresnelPower: 2,
    fresnelIntensity: 0.5,
    fresnelColor: '#FFFFFF',
    iridescenceEnabled: false,
    iridescenceIntensity: 0.5,
    iridescenceSpeed: 1,
    bloomIntensity: 0,
    bloomThreshold: 0.7,
    chromaticAberration: 0,
    shapeType: 'plane',
    shapeRotationX: 0,
    shapeRotationY: 0,
    shapeRotationZ: 0,
    shapeAutoRotateSpeedX: 0,
    shapeAutoRotateSpeedY: 0,
    sphereRadius: 15,
    torusRadius: 15,
    torusTube: 5,
    cylinderRadius: 10,
    cylinderHeight: 40,
    planeBend: 0,
    planeTwist: 0,
    silhouetteFade: 0.25,
    cylinderFade: 0.08,
    ribbonFade: 0.05,
    cameraLock: true,
    cameraX: 0,
    cameraY: 0,
    cameraZ: 0,
    cameraRotationX: 0,
    cameraRotationY: 0,
    cameraRotationZ: 0,
    cameraZoom: 1,
  };

  if (gradientRef.value) {
    gradientInstance = new NeatGradient({
      ref: gradientRef.value,
      ...config
    });
    
    window.addEventListener("scroll", handleScroll);
  }
});

onBeforeUnmount(() => {
  window.removeEventListener("scroll", handleScroll);
  if (gradientInstance && typeof gradientInstance.destroy === 'function') {
    gradientInstance.destroy();
  }
});
</script>

<template>

  <div 
    id="app-root-container" 
    class="portfolio-wrapper portfolio-flashlight-lens position-relative min-vh-100 w-100"
    :style="{ '--x': mouseX + 'px', '--y': mouseY + 'px' }"
    @mousemove="handleFlashlightMove"
  >
    <!-- Background Animated Canvas Matrix Track -->
    <canvas ref="gradientRef" id="gradient" class="position-fixed top-0 start-0 w-100 h-100 z-0"></canvas>

    <!-- Foreground Contents -->
    <div class="position-relative z-1">
      <Navbar />
      <div class="container pt-5">
        <About />
        <Projects />
        <Tools />
        <Contact />
      </div>
      <Footer />
    </div>
  </div>
</template>

<style scoped>
.portfolio-wrapper {
  background-color: #010101;
}

#gradient {
  pointer-events: none;
  object-fit: cover;
}

/*FLASHLIGHT LENS OVERLAY ENGINE*/
.portfolio-flashlight-lens::before {
  content: '';
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  pointer-events: none; 
  z-index: 1; 
  background: radial-gradient(
    500px circle at var(--x) var(--y), 
    rgba(95, 149, 152, 0.12), 
    transparent 80%
  );
}
</style>


<style>

a[href*="firecms.co"],
div[style*="position: absolute"][style*="bottom"],
div[style*="z-index"][style*="fixed"] a {
  display: none !important;
  visibility: hidden !important;
  opacity: 0 !important;
  pointer-events: none !important;
  height: 0 !important;
  width: 0 !important;
  overflow: hidden !important;
}
</style>