<script setup lang="ts">
import { useTimestamp, watchThrottled } from '@vueuse/core';
import { computed, ref, watchEffect } from 'vue';
import JSConfetti from 'js-confetti';

const meetingTimestamp = 1714636200000; // Thursday, 2 May 2024, 9:50am

const { timestamp, pause } = useTimestamp({ controls: true });

const timeDiff = computed(() => meetingTimestamp - timestamp.value);

const seconds = computed(() => Math.ceil(timeDiff.value / 1000));
const minutes = computed(() => Math.floor(seconds.value / 60));
const hours = computed(() => Math.floor(minutes.value / 60));
const days = computed(() => Math.floor(hours.value / 24));

const hoursLeft = computed(() => hours.value - days.value * 24);
const minutesLeft = computed(() => minutes.value - hours.value * 60);
const secondsLeft = computed(() => seconds.value - minutes.value * 60);

const confetti = new JSConfetti();

const showConfetti = () => confetti.addConfetti();

const counter = ref(0);

watchThrottled(
  timeDiff,
  (newVal) => {
    if (newVal > 0) return;
    showConfetti();
    counter.value++;
  },
  { throttle: 1000 }
);

watchEffect(() => {
  if (counter.value >= 3) pause();
});
</script>

<template>
  <div class="container">
    <header>
      <h1 class="text-center">
        Wannsee, Wannsee<br />
        Wann seh' ich dich endlich wieder
      </h1>
    </header>
    <main class="text-center">
      <div v-if="timeDiff > 0">
        <p>Gregor sieht seine Klasse wieder in:</p>
        <p>
          {{ days }} {{ days === 1 ? 'Tag' : 'Tage' }}, {{ hoursLeft }} {{ hoursLeft === 1 ? 'Stunde' : 'Stunden' }},
          {{ minutesLeft }} {{ minutesLeft === 1 ? 'Minute' : 'Minuten' }}, {{ secondsLeft }}
          {{ secondsLeft === 1 ? 'Sekunde' : 'Sekunden' }}
        </p>
      </div>
      <div v-else>
        <p class="success">Gregor der Rest der ME21b sind wieder vereint!</p>
        <p>Richtige 🦈-Mentalität mal wieder</p>
      </div>
    </main>
  </div>
</template>

<style scoped lang="scss">
.container {
  margin-block: 2rem;
}

header {
  margin-block-end: 2rem;
}

.text-center {
  text-align: center;
}

.success {
  font-size: larger;
}
</style>
