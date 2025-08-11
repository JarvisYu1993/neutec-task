<template>
  <div class="container">
    <div
        class="grid"
        :style="{
        gridTemplateColumns: `repeat(${size}, 1fr)`,
        gridTemplateRows: `repeat(${size}, 1fr)`
      }"
    >
      <div
          v-for="index in totalCells"
          :key="`${index}-${refreshKey}`"
          class="cell"
      >
        <svg class="square" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid meet">
          <!-- 矩形輪廓 -->
          <path
              v-show="shape === 'rectangle'"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              class="track"
          />
          <!-- 尖底愛心輪廓 -->
          <path
              v-show="shape === 'heart'"
              d="M50,30 C50,15 30,5 20,20 C10,35 25,55 50,70 C75,55 90,35 80,20 C70,5 50,15 50,30 Z"
              class="track"
          />

          <!-- 矩形動畫 -->
          <path
              v-show="isActive(index) && shape === 'rectangle'"
              class="runner runner1 rectangle"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              pathLength="318"
          />
          <path
              v-show="isActive(index) && shape === 'rectangle'"
              class="runner runner2 rectangle"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              pathLength="318"
          />

          <!-- 愛心動畫 -->
          <path
              v-show="isActive(index) && shape === 'heart'"
              class="runner runner1 heart"
              d="M50,30 C50,15 30,5 20,20 C10,35 25,55 50,70 C75,55 90,35 80,20 C70,5 50,15 50,30 Z"
              pathLength="220"
          />
          <path
              v-show="isActive(index) && shape === 'heart'"
              class="runner runner2 heart"
              d="M50,30 C50,15 30,5 20,20 C10,35 25,55 50,70 C75,55 90,35 80,20 C70,5 50,15 50,30 Z"
              pathLength="220"
          />
        </svg>
      </div>
    </div>

    <!-- 控制區 -->
    <div class="controls">
      <div class="controls-btn">
        <button
            v-for="n in [1, 3, 5, 10]"
            :key="n"
            :class="{ active: size === n }"
            @click="setSize(n)"
        >
          {{ n }}x{{ n }}
        </button>
      </div>
      <div class="controls-radio">
        <label>
          <input type="radio" value="all" v-model="mode"/> All
        </label>
        <label>
          <input type="radio" value="random" v-model="mode"/> Random
        </label>
      </div>
      <div class="controls-shape">
        <label>
          <input type="radio" value="rectangle" v-model="shape"/> Rectangle
        </label>
        <label>
          <input type="radio" value="heart" v-model="shape"/> Heart
        </label>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const size = ref(1);
const mode = ref("all");
const shape = ref("rectangle");
const randomActiveSet = ref(new Set());
const refreshKey = ref(0);

const totalCells = computed(() => size.value * size.value);

const generateRandomActive = () => {
  const total = totalCells.value;
  const count = Math.max(1, Math.floor(total * 0.6));
  const newSet = new Set();

  while (newSet.size < count) {
    const randomIndex = Math.floor(Math.random() * total) + 1;
    newSet.add(randomIndex);
  }

  randomActiveSet.value = newSet;
};

const isActive = (index) => {
  if (mode.value === "all") return true;
  return randomActiveSet.value.has(index);
};

const setSize = (n) => {
  size.value = n;
  refreshKey.value++;
};

watch(mode, (newMode) => {
  if (newMode === "random") {
    generateRandomActive();
  }
  refreshKey.value++;
});

watch(shape, () => {
  refreshKey.value++;
});

watch(totalCells, () => {
  if (mode.value === "random") {
    generateRandomActive();
  }
});

generateRandomActive();
</script>

<style>
body {
  margin: 0;
  background: black;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: white;
  height: 100vh;
  width: 100vw;
  padding: 20px;
  box-sizing: border-box;
}

.grid {
  display: grid;
  width: min(80vw, 80vh);
  height: min(80vw, 80vh);
  gap: 2px;
  margin: 20px 0;
}

.cell {
  width: 100%;
  height: 100%;
  background: #111;
  border-radius: 8px;
  overflow: hidden;
}

.square {
  width: 100%;
  height: 100%;
}

.track {
  fill: none;
  stroke: #333;
  stroke-width: 1.5;
}

.runner {
  fill: none;
  stroke: #ffffff;
  stroke-width: 1.5;
  stroke-linecap: round;
  opacity: 0.8;
}

/* 矩形動畫 */
.runner.rectangle {
  stroke-dasharray: 18 300;
}

.runner1.rectangle {
  animation: chase-rectangle 2.5s ease-in-out infinite;
  animation-delay: 0s;
}

.runner2.rectangle {
  animation: chase-rectangle 2.5s ease-in-out infinite;
  animation-delay: 1.25s;
}

/* 尖底愛心動畫 */
.runner.heart {
  stroke-dasharray: 14 206; /* 總長 220，光條 14 */
}

.runner1.heart {
  animation: chase-heart 2.8s infinite;
  animation-delay: 0s;
}

.runner2.heart {
  animation: chase-heart 2.8s infinite;
  animation-delay: 1.4s;
}

/* 矩形追逐 */
@keyframes chase-rectangle {
  0% { stroke-dashoffset: 0; animation-timing-function: ease-out; }
  24% { stroke-dashoffset: -75; animation-timing-function: ease-in; }
  25% { stroke-dashoffset: -79; animation-timing-function: ease-out; }
  49% { stroke-dashoffset: -154; animation-timing-function: ease-in; }
  50% { stroke-dashoffset: -158; animation-timing-function: ease-out; }
  74% { stroke-dashoffset: -233; animation-timing-function: ease-in; }
  75% { stroke-dashoffset: -237; animation-timing-function: ease-out; }
  99% { stroke-dashoffset: -312; animation-timing-function: ease-in; }
  100% { stroke-dashoffset: -318; }
}

/* 愛心追逐（尖角加速效果） */
@keyframes chase-heart {
  0%   { stroke-dashoffset: 0;   opacity: 1; animation-timing-function: ease-out; }
  15%  { stroke-dashoffset: -37; opacity: 1; animation-timing-function: ease-in-out; }
  30%  { stroke-dashoffset: -74; opacity: 1; animation-timing-function: ease-in; }
  50%  { stroke-dashoffset: -120;opacity: 1; animation-timing-function: ease-out; } /* 左心尖 */
  65%  { stroke-dashoffset: -157;opacity: 1; animation-timing-function: ease-in-out; }
  80%  { stroke-dashoffset: -203;opacity: 1; animation-timing-function: ease-in; } /* 右心尖 */
  100% { stroke-dashoffset: -240;opacity: 1; }
}

.controls {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 15px;
}

.controls-btn {
  display: flex;
  gap: 8px;
}

.controls-btn button {
  padding: 8px 16px;
  background: #333;
  color: white;
  border: 1px solid #555;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  transition: all 0.2s;
}

.controls-btn button.active {
  background: #0066cc;
  border-color: #0088ff;
}

.controls-btn button:hover {
  background: #444;
}

.controls-btn button.active:hover {
  background: #0077dd;
}

.controls-radio {
  display: flex;
  gap: 20px;
}

.controls-shape {
  display: flex;
  gap: 20px;
}

.controls-radio label,
.controls-shape label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
}

.controls-radio input[type="radio"],
.controls-shape input[type="radio"] {
  width: 16px;
  height: 16px;
}
</style>
