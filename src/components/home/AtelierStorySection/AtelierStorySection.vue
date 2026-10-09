<script setup>
import Icon from '../../shared/Icon/Icon.vue'
defineEmits(['navigate'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
</script>

<template>
  <section class="atelier-section reveal-on-scroll">
    <div class="atelier-copy">
      <p class="eyebrow">Inside the atelier</p>
      <h2>Made slowly.<br /><em>Worn often.</em></h2>
      <p>Each strand is composed by eye, hand-knotted for graceful movement, and finished in our light-filled atelier. It is a quiet process that lets every pearl find its place.</p>
      <button class="button button--ivory" type="button" @click="$emit('navigate', '/story')">Discover our craft <Icon name="arrow-right" :size="17" /></button>
    </div>
    <div class="atelier-image">
      <img :src="asset('images/atelier-craft.webp')" alt="Artisan hand-knotting a strand of freshwater pearls" loading="lazy" />
      <span class="image-index">Atelier note · 04</span>
    </div>
  </section>
</template>
