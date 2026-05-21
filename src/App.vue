<script setup lang="ts">
import { ref, computed, onUnmounted } from 'vue';

// Timer States
const WORK_TIME = ref(20);
const REST_TIME = ref(10);
const ROUNDS = ref(8);

const currentRound = ref(1);
const timeLeft = ref(WORK_TIME.value);
const isWorking = ref(true);
const isActive = ref(false);
const timer = ref<number | null>(null);

const displayTime = computed(() => {
  const minutes = Math.floor(timeLeft.value / 60);
  const seconds = timeLeft.value % 60;
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
});

const startTimer = () => {
  if (isActive.value) return;
  isActive.value = true;
  
  timer.value = window.setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--;
    } else {
      switchPhase();
    }
  }, 1000);
};

const pauseTimer = () => {
  if (timer.value) {
    clearInterval(timer.value);
    timer.value = null;
    isActive.value = false;
  }
};

const resetTimer = () => {
  pauseTimer();
  currentRound.value = 1;
  isWorking.value = true;
  timeLeft.value = WORK_TIME.value;
};

const switchPhase = () => {
  if (isWorking.value) {
    isWorking.value = false;
    timeLeft.value = REST_TIME.value;
  } else {
    if (currentRound.value < ROUNDS.value) {
      currentRound.value++;
      isWorking.value = true;
      timeLeft.value = WORK_TIME.value;
    } else {
      pauseTimer();
      alert("Workout Complete!");
    }
  }
};

onUnmounted(() => {
  if (timer.value) clearInterval(timer.value);
});
</script>

<template>
  <div class="section">
    <div class="container has-text-centered">
      <h1 class="title is-2">Tabata Timer</h1>
      
      <div class="card my-5">
        <div class="card-content">
          <div :class="['notification', isWorking ? 'is-primary' : 'is-link']">
            <h2 class="subtitle is-4">{{ isWorking ? 'WORK' : 'REST' }}</h2>
            <p class="timer-display">{{ displayTime }}</p>
            <p class="subtitle is-6">Round {{ currentRound }} / {{ ROUNDS }}</p>
          </div>
        </div>
      </div>

      <div class="columns is-mobile is-centered">
        <div class="column is-narrow">
          <button v-if="!isActive" class="button is-success is-large" @click="startTimer">
            <span class="icon"><i class="fas fa-play"></i></span>
            <span>START</span>
          </button>
          <button v-else class="button is-warning is-large" @click="pauseTimer">
            <span>PAUSE</span>
          </button>
        </div>
        <div class="column is-narrow">
          <button class="button is-danger is-large" @click="resetTimer">
            <span>RESET</span>
          </button>
        </div>
      </div>

      <hr />

      <div class="box has-text-left">
        <h3 class="subtitle is-5">Settings</h3>
        <div class="field">
          <label class="label">Work Time (sec)</label>
          <div class="control">
            <input class="input" type="number" v-model.number="WORK_TIME" :disabled="isActive">
          </div>
        </div>
        <div class="field">
          <label class="label">Rest Time (sec)</label>
          <div class="control">
            <input class="input" type="number" v-model.number="REST_TIME" :disabled="isActive">
          </div>
        </div>
        <div class="field">
          <label class="label">Rounds</label>
          <div class="control">
            <input class="input" type="number" v-model.number="ROUNDS" :disabled="isActive">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.timer-display {
  font-size: 4rem;
  font-weight: bold;
}
</style>
