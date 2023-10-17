<script setup lang="ts">
import { ref, Ref } from 'vue';

const timeContainer: Ref<any> = ref({});
const minutesSet: Ref<number> = ref(25);
const minutes: Ref<number> = ref(25);
const seconds: Ref<number> = ref(0);
const completed: Ref<boolean> = ref(false);
const timerRunning: Ref<boolean> = ref(false);
const timerName: Ref<string> = ref('');
const notes: Ref<string> = ref('');

function formatTime(val: number): string {
  return val < 10 ? `0${val}` : `${val}`;
}

function startTimer() {
  if (minutesSet.value) {
    minutes.value = minutesSet.value;
  }
  timerRunning.value = true;
  timeContainer.value = setInterval(() => {
    progressTime();
  }, 1000);
  sessionStorage.setItem(
    'timerName',
    JSON.stringify({
      name: timerName.value,
      timestamp: new Date().getTime(),
      timeSet: minutesSet.value,
    })
  );
}

function progressTime() {
  if (seconds.value === 0 && minutes.value > 0) {
    minutes.value -= 1;
    seconds.value = 59;
  } else if (seconds.value === 0 && minutes.value === 0) {
    clearInterval(timeContainer.value);
    completed.value = true;
    timerRunning.value = false;
  } else {
    seconds.value -= 1;
  }
}

function stopTimer() {
  clearInterval(timeContainer.value);
  timerRunning.value = false;
  minutes.value = minutesSet.value;
  seconds.value = 0;
}
</script>

<template>
  <div v-if="!timerRunning">
    <h2 class="m-3 text-3xl">Pomo Nodes</h2>
    <div class="m-3 h-0.5 w-auto bg-white"></div>
    <div>
      <input
        class="p-2 m-1 rounded-md"
        type="text"
        v-model="timerName"
        placeholder="insert name"
      />
    </div>
    <div>
      <input
        class="p-2 m-1 rounded-md"
        type="number"
        placeholder="input minutes"
        v-model="minutesSet"
      />
    </div>
    <button class="m-1" @click="startTimer">Start Timer</button>
  </div>

  <div v-else>
    <h3 class="m-1" v-if="timerRunning">{{ timerName }}</h3>

    <p v-if="timerRunning" class="timer m-1">
      {{ formatTime(minutes) }}:{{ formatTime(seconds) }}
    </p>

    <div>
      <textarea
        class="m-1 rounded-md p-2"
        v-if="timerRunning"
        name="notes"
        id="notes"
        cols="30"
        rows="7"
        :v-model="notes"
      ></textarea>
    </div>
    <button class="m-1" @click="stopTimer">Stop Timer</button>
  </div>
</template>

<style scoped>
.timer {
  font-family: monospace;
  font-size: 3rem;
  margin: 0;
}
</style>
