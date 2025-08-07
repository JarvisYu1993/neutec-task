<template>
  <div class="container">
    <!-- 動態格子 -->
    <div
        class="grid"
        :style="{
        gridTemplateColumns: `repeat(${size}, 1fr)`,
        gridAutoRows: `calc(min(100vw, 100vh) / ${size})`
      }"
    >
      <div v-for="index in totalCells" :key="index" class="cell">
        <svg class="square" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid meet">
          <!-- 邊框 -->
          <path
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
              class="track"
          />
          <!-- 第一條線 -->
          <path
              v-show="isActive(index)"
              class="runner runner1"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
          />
          <!-- 第二條線 -->
          <path
              v-show="isActive(index)"
              class="runner runner2"
              d="M15 5 H85 Q95 5 95 15 V85 Q95 95 85 95 H15 Q5 95 5 85 V15 Q5 5 15 5 Z"
          />
        </svg>
      </div>
    </div>
    <!-- 控制區 -->
    <div class="controls">
      <div class="controls-btn">
        <button v-for="n in [1, 3, 5, 10]" :key="n" @click="setSize(n)">
          {{ n }}x{{ n }}
        </button>
      </div>
      <div class="controls-radio">
        <label>
          <input type="radio" value="all" v-model="mode" /> All
        </label>
        <label>
          <input type="radio" value="random" v-model="mode" /> Random
        </label>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from "vue";

const size = ref(3);
const mode = ref("all");

const totalCells = computed(() => size.value * size.value);

const isActive = (index) => {
  if (mode.value === "all") return true;
  return index % 2 === 0;
};

const setSize = (n) => {
  size.value = n;
};
</script>

<style>
body {
  margin: 0;
  background: black;
}
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: white;
  height: 100%;
  width: 100vw;
  padding: 10px;
  box-sizing: border-box;
}

.controls {
  width: 100%;
  padding: 10px;
  display: flex;
  flex-direction: column;
}
.controls-btn {
  display: flex;
  gap: 4px;
}

.controls-radio {
  display: flex;
  padding: 10px 0;
}

.grid {
  display: grid;
  flex: 1;
  width: 100%;
  height: 100%;
  gap: 0;
}

.cell {
  width: 100%;
}

.square {
  width: 100%;
  height: 100%;
}

.track {
  fill: none;
  stroke: #555;
  stroke-width: 2;
}

.runner {
  fill: none;
  stroke: white;
  stroke-width: 2;
  stroke-linecap: round;
  stroke-dasharray: 20 380;
  stroke-dashoffset: -100;
  animation: chase 2s linear infinite;
  /* 全部格子共用同一條動畫 */
}

.runner2 {
  animation-delay: 1s; /* 第二條延遲半圈 */
}

@keyframes chase {
  0% {
    stroke-dashoffset: -100;
    animation-timing-function: ease-in;
  }
  25% {
    stroke-dashoffset: -200;
    animation-timing-function: linear;
  }
  26% {
    animation-timing-function: ease-in;
  }
  50% {
    stroke-dashoffset: -300;
    animation-timing-function: linear;
  }
  51% {
    animation-timing-function: ease-in;
  }
  75% {
    stroke-dashoffset: -400;
    animation-timing-function: linear;
  }
  76% {
    animation-timing-function: ease-in;
  }
  100% {
    stroke-dashoffset: -500;
    animation-timing-function: linear;
  }
}
</style>
