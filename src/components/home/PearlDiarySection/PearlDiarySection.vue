<script setup>
import Icon from '../../shared/Icon/Icon.vue'
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const diaryTiles = [
  { image: 'images/57bb56fd24b9c48165287894957d3cb1.jpg', label: 'The muse', shape: 'portrait' },
  { image: 'images/gift-presentation.webp', label: 'Born of water', shape: 'wide' },
  { image: 'images/16fd79ab08c0b64a38281a2bfa262abf.jpg', label: 'The details', shape: 'square' },
  { image: 'images/lunara-marquee-06-bracelets.webp', label: 'The art of giving', shape: 'wide' },
  { image: 'images/atelier-craft.webp', label: 'Made by hand', shape: 'landscape' },
]
</script>

<template>
  <section class="pearl-diary reveal-on-scroll">
    <div class="pearl-diary__heading container">
      <div><p class="eyebrow">The pearl diary</p><h2>Seen in a softer light</h2></div>
      <p>Campaign moments, atelier details and the natural forms that continue to inspire us.</p>
    </div>
    <div class="diary-grid">
      <figure v-for="tile in diaryTiles" :key="tile.label" :class="`diary-tile diary-tile--${tile.shape}`">
        <img :src="asset(tile.image)" :alt="tile.label" loading="lazy" />
        <figcaption>{{ tile.label }} <Icon name="arrow-up-right" :size="14" /></figcaption>
      </figure>
    </div>
  </section>
</template>
