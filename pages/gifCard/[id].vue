<template>
  <div 
    :class="[
      'flex items-center justify-center min-h-screen transition-colors duration-700',
      flipped 
        ? 'bg-gradient-to-br from-rose-50 to-rose-200' 
        : 'bg-gradient-to-br from-slate-50 to-sky-200'
    ]"
  >
    <div 
      ref="cardContainer" 
      class="relative w-80 h-52 perspective-1000 cursor-pointer group"
      @click="flipCard"
      @mouseenter="hoverCard"
      @mouseleave="unhoverCard"
    >
      <div ref="cardInner" class="w-full h-full relative transform-style-preserve-3d">

        <!-- Lado frontal: fondo slate-900 y logo -->
        <div class="absolute w-full h-full backface-hidden bg-slate-900 rounded-xl shadow-2xl flex items-center justify-center">
          <img src="/img/logoRn.png" class="w-32" />
        </div>

        <!-- Lado trasero: mensaje -->
        <div class="absolute w-full h-full backface-hidden transform rotate-y-180 rounded-xl shadow-2xl bg-rose-300 text-white flex flex-col justify-between p-6 text-center">

          <div class="relative w-full h-full">
            <!-- Imágenes decorativas en las esquinas -->
            <img src="/img/Flower1.png" class="w-28 absolute -top-10 -left-12 rotate-[10deg]" />
            <img src="/img/Flower2.png" class="w-28 absolute -top-10 -right-10 -rotate-[12deg]" />
            <img src="/img/Heart.png" class="w-16 absolute -bottom-7 -left-10 rotate-[5deg]" />
            <img src="/img/Heart2.png" class="w-20 absolute -bottom-10 -right-10 -rotate-[8deg]" />

            <!-- Contenido del mensaje -->
            <div class="flex flex-col items-center justify-center text-center px-6 py-10">
              <h1 class="text-2xl font-extrabold text-rose-500 mb-3 tracking-wider uppercase">
                ¡Felicidades {{ card?.to ?? '...' }}!
              </h1>
              <p class="text-base text-slate-200 leading-relaxed max-w-md">
                {{ card?.description ?? 'Te deseo un día increíble lleno de amor y alegría.' }}
              </p>
            </div>
          </div>

          <!-- Firma -->
          <div>
            <p class="text-sm text-rose-500 italic mb-4">
              Con cariño, @really.nails
            </p>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script setup>
import { gsap } from 'gsap'
const cardInner = ref(null)
const cardContainer = ref(null)
const flipped = ref(false)

const route = useRoute()
const id = route.params.id
const supabase = useSupabaseClient()
const card = ref(null)

const flipCard = () => {
  gsap.to(cardInner.value, {
    rotateY: flipped.value ? 0 : 180,
    duration: 0.8,
    ease: 'back.inOut(1.7)',
  })
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

async function fetchCard() {
  const { data, error } = await supabase
    .from('gif_cards')
    .select('*')
    .eq('id', id)
    .single()
  if (!error) card.value = data
}

onMounted(() => {
  fetchCard()

  gsap.from(cardContainer.value, {
    y: -80,
    opacity: 0,
    scale: 0.8,
    duration: 1.5,
    ease: 'elastic.out(1, 0.5)',
    delay: 0.3,
  })

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
</style>
