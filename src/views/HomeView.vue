<template>
  <div 
    ref="containerRef"
    class="flex justify-center items-center min-h-screen bg-gray-100 overflow-hidden"
    @mousemove="handleMouseMove"
  >
    <div class="flex space-x-4">
      <div 
        v-for="(card, index) in cards" 
        :key="index"
        ref="cardRefs"
        class="w-64 h-96 bg-white shadow-xl rounded-2xl transform transition-transform duration-300"
        :class="card.classes"
      >
        <div class="p-6">
          <h2 class="text-2xl font-bold mb-4">Card {{ index + 1 }}</h2>
          <p>Interactive card with mouse-responsive animation</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { gsap } from 'gsap'

const containerRef = ref(null)
const cardRefs = ref([])

const cards = [
  { classes: 'bg-blue-100' },
  { classes: 'bg-green-100' },
  { classes: 'bg-purple-100' },
  { classes: 'bg-red-100' }
]

let mouseTl = null

const handleMouseMove = (event) => {
  if (!containerRef.value) return

  const containerRect = containerRef.value.getBoundingClientRect()
  const mouseX = event.clientX - containerRect.left
  const mouseY = event.clientY - containerRect.top
  const containerWidth = containerRect.width
  const containerHeight = containerRect.height

  // Normaliser la position de la souris (0 à 1)
  const normalizedX = mouseX / containerWidth
  const normalizedY = mouseY / containerHeight

  // Nettoyer l'ancienne timeline si elle existe
  if (mouseTl) {
    mouseTl.kill()
  }

  // Créer une nouvelle timeline
  mouseTl = gsap.timeline()

  cardRefs.value.forEach((cardEl, index) => {
    // Calculer l'intensité du mouvement basée sur la position de la souris
    const multiplier = 1 - Math.abs(normalizedX - (index / (cardRefs.value.length - 1)))
    const xMovement = (normalizedX - 0.5) * 100 * multiplier
    const yMovement = (normalizedY - 0.5) * 100 * multiplier

    mouseTl.to(cardEl, {
      x: xMovement,
      y: yMovement,
      rotation: xMovement * 0.1,
      ease: 'elastic.out(0.5, 0.3)', // Effet bounce
      duration: 0.8
    }, 0)
  })
}

onMounted(() => {
  // Animation initiale de l'entrée des cartes
  gsap.from(cardRefs.value, {
    opacity: 0,
    y: 100,
    stagger: 0.2,
    ease: 'back.out(1.7)'
  })
})

onUnmounted(() => {
  // Nettoyer la timeline
  if (mouseTl) {
    mouseTl.kill()
  }
})
</script> 