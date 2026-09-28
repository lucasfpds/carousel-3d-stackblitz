<script setup>
import { ref, computed, onBeforeUnmount } from "vue";

const N = 8;
const ANGLE_STEP = 360 / N;
const SLIDE_W = 200;
const radius = SLIDE_W / 2 / Math.tan(Math.PI / N);

const theta = ref(0);
const omega = ref(0);
const dragging = ref(false);
let pointerId = null;
let lastX = 0;
let lastTime = 0;
let rafId = null;
let snapping = false;

const slides = Array.from({ length: N }, (_, i) => ({
  id: i,
  color: `hsl(${(i * 360) / N}, 70%, 55%)`,
}));

const carouselStyle = computed(() => ({
  transform: `rotateY(${theta.value}deg)`,
  transition: snapping.value ? "transform 0.4s ease-out" : "none",
}));

function slideStyle(i) {
  return {
    transform: `rotateY(${i * ANGLE_STEP}deg) translateZ(${radius}px)`,
    background: slides[i].color,
  };
}

const frontIndex = computed(() => {
  const norm = ((-theta.value % 360) + 360) % 360;
  return Math.round(norm / ANGLE_STEP) % N;
});

function onPointerDown(e) {
  dragging.value = true;
  pointerId = e.pointerId;
  lastX = e.clientX;
  lastTime = performance.now();
  snapping.value = false;
  omega.value = 0;
  e.target.setPointerCapture(e.pointerId);
  if (rafId) {
    cancelAnimationFrame(rafId);
    rafId = null;
  }
}

function onPointerMove(e) {
  if (!dragging.value || e.pointerId !== pointerId) return;
  const dx = e.clientX - lastX;
  const now = performance.now();
  const dt = now - lastTime;
  if (dt > 0) omega.value = dx / dt * 16;
  theta.value += dx * 0.3;
  lastX = e.clientX;
  lastTime = now;
}

function onPointerUp(e) {
  if (!dragging.value || e.pointerId !== pointerId) return;
  dragging.value = false;
  inertia();
}

function inertia() {
  const step = () => {
    if (Math.abs(omega.value) < 0.05) {
      snap();
      return;
    }
    theta.value += omega.value;
    omega.value *= 0.95;
    rafId = requestAnimationFrame(step);
  };
  rafId = requestAnimationFrame(step);
}

function snap() {
  snapping.value = true;
  const nearest = Math.round(theta.value / ANGLE_STEP) * ANGLE_STEP;
  theta.value = nearest;
  setTimeout(() => {
    snapping.value = false;
    rafId = null;
  }, 400);
}

onBeforeUnmount(() => {
  if (rafId) cancelAnimationFrame(rafId);
});
</script>

<template>
  <main class="app">
    <h1>Carrossel 3D</h1>
    <p class="hint">Arraste para girar · solte para inércia + snap</p>
    <div class="stage" @pointerdown="onPointerDown" @pointermove="onPointerMove" @pointerup="onPointerUp" @pointercancel="onPointerUp">
      <div class="carousel" :style="carouselStyle">
        <div
          v-for="(s, i) in slides"
          :key="s.id"
          class="slide"
          :class="{ 'slide--front': i === frontIndex }"
          :style="slideStyle(i)"
        >
          <span>{{ i + 1 }}</span>
        </div>
      </div>
      <div class="floor"></div>
    </div>
    <p class="front-label">Slide frontal: <strong>{{ frontIndex + 1 }}</strong></p>
  </main>
</template>
