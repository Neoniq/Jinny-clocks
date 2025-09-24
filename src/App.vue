<script setup lang="ts">
import { ref } from 'vue'

const age = ref(77)
const showModal = ref(false)
const modalTitle = ref('')
const inputValue = ref('')
const isIncrease = ref(true)
const isClockAnimating = ref(false)
const isModalClosing = ref(false)

const openModal = (increase: boolean) => {
  isIncrease.value = increase
  modalTitle.value = increase
    ? 'На сколько лет увеличить возраст?'
    : 'На сколько лет уменьшить возраст?'
  inputValue.value = ''
  showModal.value = true
}

const closeModal = () => {
  isModalClosing.value = true
  setTimeout(() => {
    showModal.value = false
    isModalClosing.value = false
  }, 300)
}

const confirmChange = () => {
  const years = parseInt(inputValue.value)
  if (!isNaN(years) && years > 0) {
    // Запускаем анимацию часов
    isClockAnimating.value = true

    // Изменяем возраст
    if (isIncrease.value) {
      age.value += years
    } else {
      age.value = Math.max(0, age.value - years)
    }

    // Останавливаем анимацию через 1.5 секунды
    setTimeout(() => {
      isClockAnimating.value = false
    }, 1500)
  }
  closeModal()
}

const increaseAge = () => {
  openModal(true)
}

const decreaseAge = () => {
  openModal(false)
}
</script>

<template>
  <div
    class="min-h-screen bg-color-background flex flex-col items-center p-4 font-primary relative overflow-hidden"
  >
    <!-- Фоновые часы -->
    <div class="background-clocks">
      <img src="/img/clocks1.png" alt="" class="clock-bg clock-1" />
      <img src="/img/clocks2.png" alt="" class="clock-bg clock-2" />
      <img src="/img/clocks3.png" alt="" class="clock-bg clock-3" />
      <img src="/img/clocks4.png" alt="" class="clock-bg clock-4" />
      <img src="/img/clocks5.png" alt="" class="clock-bg clock-5" />
      <img src="/img/clocks6.png" alt="" class="clock-bg clock-6" />
      <img src="/img/clocks66.png" alt="" class="clock-bg clock-7" />
      <img src="/img/clocks7.png" alt="" class="clock-bg clock-8" />
      <img src="/img/clocks8.png" alt="" class="clock-bg clock-9" />
    </div>

    <!-- Заголовок -->
    <div class="mb-1 z-10">
      <h1 class="page-title">Часы <span class="accent-text">не</span>потерянного времени</h1>
    </div>

    <!-- Часы -->
    <div class="mb-1 relative w-64 h-64 z-10">
      <img
        src="/img/mainClock.svg"
        alt="Часы"
        class="w-64 h-64 object-contain"
        :class="{ 'clock-spin': isClockAnimating }"
      />
      <!-- Большая стрелка (часовая) -->
      <img src="/img/largeArrow.png" alt="Часовая стрелка" class="large-arrow" />
      <!-- Малая стрелка (минутная) -->
      <img src="/img/smallArrow.png" alt="Минутная стрелка" class="small-arrow" />
    </div>

    <!-- Кнопки управления -->
    <div class="flex gap-12 mb-1 z-10">
      <button
        @click="increaseAge"
        class="w-24 h-24 bg-color-accent hover:bg-color-dark-green text-white rounded-full transition-colors shadow-lg flex items-center justify-center hover:scale-110 transform duration-200"
      >
        <img src="/img/plus.svg" alt="+" class="w-12 h-12" />
      </button>
      <button
        @click="decreaseAge"
        class="w-24 h-24 bg-color-primary hover:bg-color-secondary text-white rounded-full transition-colors shadow-lg flex items-center justify-center hover:scale-110 transform duration-200"
      >
        <img src="/img/minus.svg" alt="-" class="w-12 h-12" />
      </button>
    </div>

    <!-- Счетчик возраста -->
    <div class="age-container mt-4 z-10">
      <h2 class="age-label">Мой возраст</h2>
      <div class="age-number">
        {{ age }}
      </div>
    </div>

    <!-- Модальное окно -->
    <div
      v-if="showModal"
      class="modal-overlay"
      :class="{ 'modal-closing': isModalClosing }"
      @click="closeModal"
    >
      <div class="modal-content" :class="{ 'modal-content-closing': isModalClosing }" @click.stop>
        <h3 class="modal-title">{{ modalTitle }}</h3>
        <input
          v-model="inputValue"
          type="number"
          inputmode="numeric"
          min="1"
          class="modal-input"
          @keyup.enter="confirmChange"
          @keyup.escape="closeModal"
          ref="modalInput"
        />
        <div class="modal-buttons">
          <button @click="closeModal" class="modal-btn modal-btn-cancel">Отмена</button>
          <button @click="confirmChange" class="modal-btn modal-btn-confirm">Применить</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.large-arrow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 48px;
  height: 120px;
  object-fit: contain;
  transform: translate(-50%, -100%) translateX(10px);
  transform-origin: bottom center;
  animation: rotate-clockwise 20s linear infinite;
}

.small-arrow {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 36px;
  height: 96px;
  object-fit: contain;
  transform: translate(-50%, -100%) translateX(10px) rotate(300deg);
  transform-origin: bottom center;
  animation: rotate-clockwise-small 45s linear infinite;
}

@keyframes rotate-clockwise {
  from {
    transform: translate(-50%, -100%) translateX(10px) rotate(0deg);
  }
  to {
    transform: translate(-50%, -100%) translateX(10px) rotate(360deg);
  }
}

@keyframes rotate-clockwise-small {
  from {
    transform: translate(-50%, -100%) translateX(10px) rotate(300deg);
  }
  to {
    transform: translate(-50%, -100%) translateX(10px) rotate(660deg);
  }
}

.age-number {
  font-family: var(--font-primary);
  font-size: 5rem;
  font-weight: 900;
  color: var(--color-primary);
  animation: bounce-gentle 3s ease-in-out infinite;
  background: linear-gradient(135deg, var(--color-dark-blue), var(--color-dark-green));
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  display: inline-block;
  transform: rotate(-2deg);
  transition: all 0.3s ease;
}

.age-number:hover {
  transform: rotate(2deg) scale(1.1);
  animation-play-state: paused;
}

@keyframes bounce-gentle {
  0%,
  100% {
    transform: rotate(-2deg) translateY(0px);
  }
  50% {
    transform: rotate(-2deg) translateY(-8px);
  }
}

.age-container {
  text-align: center;
  padding: 30px 40px;
  background: linear-gradient(135deg, rgba(255, 255, 255, 0.1), rgba(255, 255, 255, 0.05));
  border-radius: 25px;
  border: 3px solid var(--color-primary);
  box-shadow:
    0 10px 30px rgba(0, 0, 0, 0.2),
    inset 0 1px 0 rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(10px);
  position: relative;
  overflow: hidden;
}

.age-container::before {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(45deg, transparent, rgba(255, 255, 255, 0.1), transparent);
  animation: shimmer 4s infinite;
  pointer-events: none;
}

.age-label {
  font-family: var(--font-display);
  font-size: 2rem;
  font-weight: 700;
  color: var(--color-primary);
  margin-bottom: 15px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
  letter-spacing: 1px;
}

.age-unit {
  font-family: var(--font-display);
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--color-secondary);
  margin-top: 10px;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.3);
  letter-spacing: 0.5px;
}

@keyframes shimmer {
  0% {
    transform: translateX(-100%) translateY(-100%) rotate(45deg);
  }
  100% {
    transform: translateX(100%) translateY(100%) rotate(45deg);
  }
}

.background-clocks {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 0;
}

.clock-bg {
  position: absolute;
  opacity: 0.9;
  animation: float 8s ease-in-out infinite;
}

.clock-1 {
  top: 10%;
  left: 5%;
  width: 80px;
  height: 80px;
  animation-delay: 0s;
}

.clock-2 {
  top: 30%;
  right: 4%;
  width: 60px;
  height: 60px;
  animation-delay: 1s;
}

.clock-3 {
  top: 60%;
  left: 8%;
  width: 70px;
  height: 70px;
  animation-delay: 2s;
}

.clock-4 {
  top: 45%;
  right: 11%;
  width: 55px;
  height: 55px;
  animation-delay: 5s;
}

.clock-5 {
  bottom: 5%;
  right: 2%;
  width: 65px;
  height: 65px;
  animation-delay: 4s;
}

.clock-6 {
  top: 12%;
  left: 77%;
  width: 90px;
  height: 90px;
  animation-delay: 3s;
}

.clock-7 {
  bottom: 5%;
  left: 2%;
  width: 75px;
  height: 75px;
  animation-delay: 6s;
}

.clock-8 {
  top: 67%;
  right: 9%;
  width: 85px;
  height: 85px;
  animation-delay: 7s;
}

.clock-9 {
  top: 35%;
  left: 4%;
  width: 50px;
  height: 50px;
  animation-delay: 8s;
}

@keyframes float {
  0%,
  100% {
    transform: translateY(0px);
  }
  25% {
    transform: translateY(-10px);
  }
  50% {
    transform: translateY(-5px);
  }
  75% {
    transform: translateY(-15px);
  }
}

.page-title {
  font-family: var(--font-display);
  font-size: 2rem;
  font-weight: 900;
  color: var(--color-primary);
  text-align: center;

  letter-spacing: 2px;
  margin: 0;
}

.accent-text {
  color: var(--color-dark-blue);
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
  backdrop-filter: blur(5px);
  animation: modal-fade-in 0.3s ease-out;
}

@keyframes modal-fade-in {
  from {
    opacity: 0;
    backdrop-filter: blur(0px);
  }
  to {
    opacity: 1;
    backdrop-filter: blur(5px);
  }
}

.modal-content {
  margin-top: 10%;
  margin-bottom: auto;
  background: var(--color-background);
  border: 4px solid var(--color-primary);
  border-radius: 20px;
  padding: 30px;
  max-width: 400px;
  width: 90%;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
  text-align: center;
  animation: modal-slide-in 0.4s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes modal-slide-in {
  from {
    opacity: 0;
    transform: translateY(-50px) scale(0.8);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

.modal-closing {
  animation: modal-fade-out 0.3s ease-in forwards;
}

.modal-content-closing {
  animation: modal-slide-out 0.3s ease-in forwards;
}

@keyframes modal-fade-out {
  from {
    opacity: 1;
    backdrop-filter: blur(5px);
  }
  to {
    opacity: 0;
    backdrop-filter: blur(0px);
  }
}

@keyframes modal-slide-out {
  from {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
  to {
    opacity: 0;
    transform: translateY(-50px) scale(0.8);
  }
}

.modal-title {
  font-family: var(--font-display);
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--color-primary);
  margin-bottom: 20px;
}

.modal-input {
  width: 100%;
  padding: 15px;
  font-size: 1.5rem;
  font-family: var(--font-primary);
  text-align: center;
  border: 3px solid var(--color-secondary);
  border-radius: 10px;
  background: white;
  color: var(--color-primary);
  margin-bottom: 20px;
  outline: none;
}

.modal-input:focus {
  border-color: var(--color-accent);
  box-shadow: 0 0 10px rgba(104, 132, 97, 0.3);
}

.modal-buttons {
  display: flex;
  gap: 15px;
  justify-content: center;
}

.modal-btn {
  padding: 12px 24px;
  font-size: 1.1rem;
  font-family: var(--font-primary);
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: all 0.3s ease;
  letter-spacing: 3px;
}

.modal-btn-cancel {
  background: var(--color-secondary);
  color: white;
}

.modal-btn-cancel:hover {
  background: var(--color-primary);
  transform: translateY(-2px);
}

.modal-btn-confirm {
  background: var(--color-accent);
  color: white;
}

.modal-btn-confirm:hover {
  background: var(--color-dark-green);
  transform: translateY(-2px);
}

/* Анимация часов при изменении возраста */
.clock-spin {
  animation: clock-spin 1.2s ease-in-out;
}

@keyframes clock-spin {
  0% {
    transform: rotate(0deg) scale(1);
    filter: brightness(1);
  }
  100% {
    transform: rotate(360deg) scale(1);
    filter: brightness(1);
  }
}
</style>
