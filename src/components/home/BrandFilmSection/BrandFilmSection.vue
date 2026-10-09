<script setup>
import { onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const film = ref(null)
const filmPaused = ref(false)
const reducedMotion = ref(false)

onMounted(() => {
  reducedMotion.value = window.matchMedia('(prefers-reduced-motion: reduce)').matches
  filmPaused.value = reducedMotion.value
})

function toggleFilm() {
  if (!film.value) return
  if (film.value.paused) {
    film.value.play().catch(() => {})
    filmPaused.value = false
  } else {
    film.value.pause()
    filmPaused.value = true
  }
}
</script>

<template>
  <section class="brand-film-section reveal-on-scroll">
    <video ref="film" :autoplay="!reducedMotion" muted loop playsinline preload="metadata" :poster="asset('images/pearl-origin.webp')" aria-label="Lunara pearl jewellery brand film">
      <source :src="asset('videos/lunara-brand-film.mp4')" type="video/mp4" />
    </video>
    <div class="brand-film-section__veil"></div>
    <div class="brand-film-section__copy">
      <p class="eyebrow">A study in light · Brand film</p>
      <h2>From water<br />to <em>heirloom.</em></h2>
      <p>A quiet portrait of lustre, movement and the hands behind every Lunara piece.</p>
    </div>
    <button class="film-control" type="button" :aria-label="filmPaused ? 'Play brand film' : 'Pause brand film'" @click="toggleFilm">
      <Icon :name="filmPaused ? 'play' : 'pause'" :size="17" />
      <span>{{ filmPaused ? 'Play film' : 'Pause film' }}</span>
    </button>
    <span class="film-time">00:13 · Silent film</span>
  </section>
</template>
