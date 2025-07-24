<script setup lang="ts">
import CheckSquares from '@/components/CheckSquares.vue';
import moment from 'moment';
import { reactive, ref } from 'vue';


const interval = ref(5)
const displayIntervalID = ref<number | null>(null)
const intervalID = ref<number | null>(null)
const blocks = reactive({
  0: 'horizontal',
  1: 'vertical',
  2: 'check'
})
const timerForm = reactive({
  mm: '00',
  ss: '00',
})
const timerDisplay = ref('00:00')
const activeBlock = ref<keyof typeof blocks>(0);
const periodTime = ref('5')


const updMin = (ev: Event) => {
  const input = ev.target as HTMLInputElement
  const digits = input.value.replace(/\D/g, '') // Убираем всё кроме цифр
  let readyValue = digits.slice(0, 2)
  if(+readyValue > 59) {
    readyValue = '59'
  }
  timerForm.mm = readyValue
  input.value = readyValue
}
const updSec = (ev: Event) => {
  const input = ev.target as HTMLInputElement
  const digits = input.value.replace(/\D/g, '') // Убираем всё кроме цифр
  let readyValue = digits.slice(0, 2)
  if(+readyValue > 59) {
    readyValue = '59'
  }
  timerForm.ss = readyValue
  input.value = readyValue
}
const updPeriod = (ev: Event) => {
  const input = ev.target as HTMLInputElement
  const digits = input.value.replace(/\D/g, '') // Убираем всё кроме цифр
  let readyValue = digits.slice(0, 2)
  if(+readyValue > 59) {
    readyValue = '59'
  }
  periodTime.value = readyValue
  input.value = readyValue
}

function play() {
  if(intervalID.value !== null) {
    clearInterval(intervalID.value)
    intervalID.value = null
    return
  }
  activeBlock.value = activeBlock.value ? 0 : 1
  intervalID.value = setInterval(() => {
    activeBlock.value = activeBlock.value ? 0 : 1
  }, interval.value * 1000)
}

function stopEffect() {
  if(intervalID.value) {
    clearInterval(intervalID.value)
    intervalID.value = null
  }
  if(displayIntervalID.value) {
    clearInterval(displayIntervalID.value)
    displayIntervalID.value = null
  }
  timerDisplay.value = '00:00'
  timerForm.mm = ''
  timerForm.ss = ''
}

const startTimer = (
  form: { mm: string; ss: string },
  onDone: () => void,
  onTick: (mm: string, ss: string) => void
) => {
  const totalMs = moment.duration({
    minutes: Number(form.mm),
    seconds: Number(form.ss),
  }).asMilliseconds()

  if (totalMs <= 0) return

  const startTime = Date.now()
  const endTime = startTime + totalMs

  let lastSecond = -1

  displayIntervalID.value = setInterval(() => {
    const now = Date.now()
    const left = endTime - now

    if (left <= 0) {
      if(displayIntervalID.value) {
        clearInterval(displayIntervalID.value)
      }
      form.mm = '00'
      form.ss = '00'
      onTick('00', '00') // финальный тик
      onDone()
      return
    }

    const duration = moment.duration(left)
    const mm = String(duration.minutes()).padStart(2, '0')
    const ss = String(duration.seconds()).padStart(2, '0')

    form.mm = mm
    form.ss = ss

    const currentSec = duration.seconds()
    if (currentSec !== lastSecond) {
      lastSecond = currentSec
      onTick(mm, ss)
    }
  }, 100)
}

function handlerPlayTimer() {
  if(intervalID.value) {
    return stopEffect()
  }
  if(Number(timerForm.mm) <= 0 && Number(timerForm.ss) <= 0) {
    timerDisplay.value = '∞:∞'
    timerForm.mm = '∞'
    timerForm.ss = '∞'
    return play()
  }
  startTimer(
    {
      mm: timerForm.mm,
      ss: timerForm.ss,
    },
    () => {
      timerDisplay.value = '00:00'
      timerForm.mm = ''
      timerForm.ss = ''
      stopEffect()
    },
   (mm: string, ss: string) => {
      timerDisplay.value = `${mm}:${ss}`
    }
  )
  timerDisplay.value = `${timerForm.mm}:${timerForm.ss}`
  timerForm.mm = ''
  timerForm.ss = ''
  play()
}

function handlerCheck() {
  stopEffect()
  if(activeBlock.value === 2) {
    return activeBlock.value = 0
  }
  activeBlock.value = 2
}

function handlerUpdPeriod() {
  if(intervalID.value) {
    clearInterval(intervalID.value)
    interval.value = Number(periodTime.value)
    intervalID.value = setInterval(() => {
      activeBlock.value = activeBlock.value ? 0 : 1
    }, interval.value * 1000)
  }
}


</script>

<template>
  <main class="home-main">

    <div class="dashboard">
      <div class="control-panel">
        <div class="rec-overlay">
          <div v-if="intervalID" class="rec-btn"></div>
        </div>
        <button @click="handlerPlayTimer">
          {{ intervalID ? 'Stop' : '▶ Play' }}
        </button>
        <div class="timer-input-block">
          <input :value="timerForm.mm" class="time-input" placeholder="mm" @input="updMin">
          <h2>:</h2>
          <input :value="timerForm.ss" class="time-input" placeholder="ss" @input.prevent="updSec">
        </div>
        <div>
          <h2 class="timer-display">
            {{ timerDisplay }}
          </h2>
        </div>
        <button @click="handlerCheck">
          {{ (activeBlock === 2) ? 'Back' : 'Check' }}
        </button>

        <div class="period-block">
          <!-- periodicity -->
          <p>periodicity: </p>
          <input
            class="period-input"
            :value="periodTime"
            placeholder="ss"
            @input="updPeriod"
          >
          <button @click="handlerUpdPeriod">
            {{ '✔' }}
          </button>
        </div>
      </div>
    </div>
    <div class="blocks-container">

      <div class="striped vertical" v-if="blocks[activeBlock] === blocks['0']"></div>
      <div class="striped horizontal" v-else-if="blocks[activeBlock] === blocks['1']"></div>
      <CheckSquares v-else-if="blocks[activeBlock] === blocks['2']" />
    </div>
  </main>
</template>

<style scoped>
button {
  background-color: transparent;
  border: 1px solid gray;
  outline: none;
  color: gray;
  padding: 4px 10px;
  font-size: 14px;
  font-family: monospace;
  border-radius: 4px;
  cursor: pointer;
}
.home-main {
  position: relative;
  width: 100%;
  height: 100%;
  border: 4px dashed rgb(59, 29, 29);
  display: flex;
  align-items: center;
  justify-content: center;
}
.timer-display {
  color: rgb(211, 201, 221);
  font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  font-weight: 400;
  margin-left: 8px;
  margin-top: 8px;
}
.dashboard {
  position: absolute;
  right: 10px;
  top: 10px;
  display: flex;
  flex-direction: column;
  gap: .5rem;
}
input {
  background-color: transparent;
  outline: none;
  border: 1px solid gray;
  color: whitesmoke;
  padding: 2px 8px;
}
.period-block {
  display: flex;
  align-items: center;
  gap: 4px;
}
.period-input {
  width: 30px;
  padding: 8px 6px;
}
.time-input {
  width: 40px !important;
}
.timer-input-block {
  display: flex;
  gap: 4px;
}
.rec-overlay {
  width: 24px;
  height: 24px;
}
.rec-btn {
  width: 65%;
  height: 65%;
  border-radius: 50%;
  background-color: orange;
}
.control-panel {
  display: flex;
  justify-content: start;
  flex-direction: column;
  padding: 0.5rem 1rem;
  height: max-content;
  /* border: 1px solid gray; */
  border-radius: 8px;
  gap: 6px;
  background-color: rgba(0, 0, 0, 0.25);
  backdrop-filter: blur(4px);
}

.blocks-container {
  width: 70%;
  height: 90%;
  border: 1px dashed rgb(59, 29, 29);
  overflow: hidden;
}

.striped {
  width: 100%;
  height: 100%;
}

.vertical {
  background: repeating-linear-gradient(
    to right,
    rgb(20, 212, 20) 0px,
    rgb(20, 212, 20) 18px,
    rgb(0, 0, 0) 10px,
    rgb(0, 0, 0) 30px
  );
}

.horizontal {
  background: repeating-linear-gradient(
    to bottom,
    red 0px,
    red 18px,
    rgb(0, 0, 0) 10px,
    rgb(0, 0, 0) 30px
  );
}
</style>
