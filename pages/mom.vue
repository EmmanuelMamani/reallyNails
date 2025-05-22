<template>
  <div class="flex items-center justify-center min-h-screen bg-gradient-to-br from-pink-50 to-purple-50">
    <div 
      ref="cardContainer" 
      class="relative w-80 h-52 perspective-1000 cursor-pointer group"
      @click="flipCard"
      @mouseenter="hoverCard"
      @mouseleave="unhoverCard"
    >
      <!-- Efecto de brillo al hacer hover -->
      <div class="absolute inset-0 rounded-xl bg-white/30 opacity-0 group-hover:opacity-100 transition-opacity duration-300"></div>
      
      <div ref="cardInner" class="w-full h-full relative transform-style-preserve-3d">
        <!-- Front -->
        <div
          class="absolute w-full h-full backface-hidden bg-gradient-to-br from-pink-400 to-pink-600 rounded-xl shadow-2xl flex flex-col items-center justify-center p-6 text-white overflow-hidden"
        >
          <!-- Confetis (generados en el cliente) -->
          <div 
            v-for="(style, i) in confettiStyles" 
            :key="'confetti-'+i"
            class="absolute confetti"
            :style="style"
          ></div>

          <div class="relative z-10 text-center">
            <div class="text-5xl mb-3">💌</div>
            <h2 class="text-2xl font-bold mb-1">Para Mamá</h2>
            <p class="text-sm opacity-90">Toca para abrir</p>
          </div>

          <div class="absolute inset-0 overflow-hidden rounded-xl">
            <div class="absolute shine -inset-y-1/2 -left-1/2 w-20 h-[200%] bg-white/30 transform rotate-12 animate-shine"></div>
          </div>
        </div>

        <!-- Inside -->
        <div
          class="absolute w-full h-full backface-hidden bg-gradient-to-br from-white to-pink-50 rounded-xl shadow-2xl transform rotate-y-180 flex flex-col justify-center items-center p-6 text-center overflow-hidden"
        >
          <div class="absolute -bottom-4 -right-4 text-6xl opacity-20 text-pink-400">🌸</div>
          <div class="absolute -top-4 -left-4 text-5xl opacity-20 text-pink-400">🌷</div>
          <div class="relative z-10">
            <h1 class="text-2xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-pink-500 to-purple-600 mb-3">
              ¡Feliz Día de la Madre!
            </h1>
            <p class="text-gray-700 mb-4">
              Gracias por tu amor incondicional y tu ternura infinita 💖
            </p>
            <p class="text-sm text-gray-500 italic">
              Con todo mi cariño
            </p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { gsap } from 'gsap'

const cardInner = ref(null)
const cardContainer = ref(null)
const flipped = ref(false)
const confettiStyles = ref([])

const flipCard = () => {
  const rotation = flipped.value ? 0 : 180
  gsap.to(cardInner.value, {
    rotateY: rotation,
    duration: 0.8,
    ease: 'back.inOut(1.7)',
  })

  if (!flipped.value) {
    gsap.to('.confetti', {
      opacity: 1,
      y: () => Math.random() * -60 - 20,
      x: () => (Math.random() - 0.5) * 40,
      scale: () => Math.random() * 0.5 + 0.5,
      duration: 1.5,
      stagger: 0.05,
      ease: 'power2.out',
    })

    setTimeout(() => {
      gsap.to('.confetti', {
        opacity: 0,
        duration: 0.5,
      })
    }, 1500)
  }

  flipped.value = !flipped.value
}

const hoverCard = () => {
  if (!flipped.value) {
    gsap.to(cardContainer.value, {
      scale: 1.05,
      duration: 0.3,
      ease: 'power2.out',
    })
  }
}

const unhoverCard = () => {
  gsap.to(cardContainer.value, {
    scale: 1,
    duration: 0.3,
    ease: 'power2.out',
  })
}

onMounted(() => {
  // Solo en cliente: genera los estilos aleatorios
  confettiStyles.value = Array.from({ length: 20 }, () => ({
    top: `${Math.random() * 100}%`,
    left: `${Math.random() * 100}%`,
    width: `${Math.random() * 8 + 4}px`,
    height: `${Math.random() * 8 + 4}px`,
    backgroundColor: `hsl(${Math.random() * 60 + 330}, 100%, ${Math.random() * 30 + 60}%)`,
    transform: `rotate(${Math.random() * 360}deg)`,
    opacity: 0,
  }))

  // Entrada inicial
  gsap.from(cardContainer.value, {
    y: -80,
    opacity: 0,
    scale: 0.8,
    duration: 1.5,
    ease: 'elastic.out(1, 0.5)',
    delay: 0.3,
  })

  // Flotación sutil
  gsap.to(cardContainer.value, {
    y: 5,
    duration: 2,
    repeat: -1,
    yoyo: true,
    ease: 'sine.inOut',
  })
})
</script>

<style scoped>
.perspective-1000 {
  perspective: 1000px;
}
.transform-style-preserve-3d {
  transform-style: preserve-3d;
}
.backface-hidden {
  backface-visibility: hidden;
}
.rotate-y-180 {
  transform: rotateY(180deg);
}

@keyframes shine {
  to {
    left: 150%;
  }
}
.animate-shine {
  animation: shine 3s ease-in-out infinite;
}

.confetti {
  position: absolute;
  border-radius: 20%;
  pointer-events: none;
}

.group:hover {
  filter: drop-shadow(0 10px 20px rgba(236, 72, 153, 0.3));
  transition: filter 0.3s ease;
}
</style>
