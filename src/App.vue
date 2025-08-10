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
          <path
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              class="track"
          />
          <path
              v-show="isActive(index)"
              class="runner runner1"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              pathLength="318"
          />
          <path
              v-show="isActive(index)"
              class="runner runner2"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              pathLength="318"
          />
        </svg>
      </div>
    </div>

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
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";

const size = ref(1);
const mode = ref("all");
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
  refreshKey.value++; // 重新渲染動畫
};

watch(mode, (newMode) => {
  if (newMode === "random") {
    generateRandomActive();
  }
  refreshKey.value++; // 模式切換時重新開始動畫
});

watch(totalCells, () => {
  if (mode.value === "random") {
    generateRandomActive();
  }
});
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
  stroke-dasharray: 18 300;
  opacity: 0.8;
}

.runner1 {
  animation: chase 2.5s ease-in-out infinite;
  animation-delay: 0s;
}

.runner2 {
  animation: chase 2.5s ease-in-out infinite;
  animation-delay: 1.25s;
  stroke: #ffffff;
}

@keyframes chase {
  0% {
    stroke-dashoffset: 0;
    opacity: 1;
    animation-timing-function: ease-out;
  }
  24% {
    stroke-dashoffset: -75;
    opacity: 1;
    animation-timing-function: ease-in;
  }
  25% {
    stroke-dashoffset: -79;
    opacity: 1;
    animation-timing-function: ease-out;
  }
  49% {
    stroke-dashoffset: -154;
    opacity: 1;
    animation-timing-function: ease-in;
  }
  50% {
    stroke-dashoffset: -158;
    opacity: 1;
    animation-timing-function: ease-out;
  }
  74% {
    stroke-dashoffset: -233;
    opacity: 1;
    animation-timing-function: ease-in;
  }
  75% {
    stroke-dashoffset: -237;
    opacity: 1;
    animation-timing-function: ease-out;
  }
  99% {
    stroke-dashoffset: -312;
    opacity: 1;
    animation-timing-function: ease-in;
  }
  100% {
    stroke-dashoffset: -318;
    opacity: 1;
  }
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

.controls-radio label {
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  font-size: 14px;
}

.controls-radio input[type="radio"] {
  width: 16px;
  height: 16px;
}
</style>
