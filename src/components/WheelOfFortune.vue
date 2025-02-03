<template>
  <div class="wheel-container">
    <div class="wheel" :style="{ transform: `rotate(${rotation}deg)` }">
      <div 
        v-for="(option, index) in options" 
        :key="index"
        class="wheel-section"
        :style="getSectionStyle(index)"
      >
      </div>
      <!-- Добавляем линии-разделители -->
      <div 
        v-for="(_, index) in options" 
        :key="`line-${index}`"
        class="divider-line"
        :style="getDividerStyle(index)"
      ></div>
    </div>
    <div class="pointer"></div>
    <button @click="spin" :disabled="isSpinning">Крутить</button>

    <!-- Модальное окно -->
    <div v-if="showModal" class="modal">
      <div class="modal-content">
        <h2>Результат</h2>
        <p>{{ selectedOption }}</p>
        <button @click="closeModal" class="modal-button">OK</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const options = ref([
  'Сосал?',
  'Сосал?',
  'Сосал?',
  'Сосал?',
  'Сосал?',
  'Сосал?',
])
const rotation = ref(0)
const isSpinning = ref(false)
const showModal = ref(false)
const selectedOption = ref('')

const getSectionStyle = (index) => {
  const sectionAngle = 360 / options.value.length
  const rotation = index * sectionAngle
  const backgroundColor = `hsl(${index * (360 / options.value.length)}, 70%, 70%)`
  return {
    transform: `rotate(${rotation}deg)`,
    backgroundColor,
    position: 'absolute',
    width: '50%',
    height: '50%',
    transformOrigin: 'right bottom',
    clipPath: 'polygon(0 0, 100% 0, 100% 100%, 0% 100%)',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center'
  }
}

const getDividerStyle = (index) => {
  const sectionAngle = 360 / options.value.length
  const rotation = index * sectionAngle
  return {
    transform: `rotate(${rotation}deg)`,
    position: 'absolute',
    width: '50%',
    height: '2px',
    background: '#333',
    transformOrigin: 'right',
    right: '50%',
    top: '50%'
  }
}

const closeModal = () => {
  showModal.value = false
}

const spin = () => {
  if (isSpinning.value) return

  isSpinning.value = true
  const extraSpins = 5
  const randomDegrees = Math.random() * 360
  const totalRotation = rotation.value + (360 * extraSpins) + randomDegrees

  rotation.value = totalRotation

  setTimeout(() => {
    isSpinning.value = false
    const normalizedRotation = totalRotation % 360
    const sectionAngle = 360 / options.value.length
    const selectedIndex = Math.floor((360 - normalizedRotation) / sectionAngle)
    selectedOption.value = options.value[selectedIndex]
    showModal.value = true
  }, 5000)
}
</script>

<style scoped>
.wheel-container {
  position: relative;
  width: 400px;
  height: 400px;
  margin: 50px auto;
}

.wheel {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  border: 2px solid #333;
  position: relative;
  transition: transform 5s cubic-bezier(0.17, 0.67, 0.12, 0.99);
  transform-origin: center center;
  overflow: hidden;
}

.divider-line {
  z-index: 2;
}

.pointer {
  position: absolute;
  top: -20px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 20px solid transparent;
  border-right: 20px solid transparent;
  border-top: 40px solid red;
  z-index: 3;
}

button {
  position: absolute;
  bottom: -60px;
  left: 50%;
  transform: translateX(-50%);
  padding: 10px 20px;
  font-size: 18px;
  cursor: pointer;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: white;
  padding: 30px;
  border-radius: 10px;
  text-align: center;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  max-width: 400px;
  width: 90%;
}

.modal-content h2 {
  margin-top: 0;
  color: #333;
  font-size: 24px;
}

.modal-content p {
  font-size: 20px;
  margin: 20px 0;
  color: #666;
}

.modal-button {
  background-color: #4CAF50;
  color: white;
  padding: 10px 30px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s;
}

.modal-button:hover {
  background-color: #45a049;
}

/* Анимация для модального окна */
.modal {
  animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
</style> 