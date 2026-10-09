<script setup>
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const categoryTiles = [
  { name: 'Necklaces', image: 'images/classic-strand.webp', note: 'Softly luminous' },
  { name: 'Earrings', image: 'images/celeste-drops.webp', note: 'Everyday light' },
  { name: 'Rings', image: 'images/solitaire-ring.webp', note: 'Modern heirlooms' },
  { name: 'Bracelets', image: 'images/twin-pearl-cuff.webp', note: 'Quiet sculpture' },
]
</script>

<template>
  <section class="section category-section container reveal-on-scroll">
    <div class="section-heading section-heading--center">
      <p class="eyebrow">Choose your light</p>
      <h2>Jewellery for every chapter</h2>
      <p>Enduring pieces, designed with the ease to be worn now and cherished for years.</p>
    </div>
    <div class="category-grid">
      <button v-for="tile in categoryTiles" :key="tile.name" class="category-card" type="button" @click="$emit('explore', tile.name)">
        <span class="category-card__image"><img :src="asset(tile.image)" :alt="`${tile.name} collection`" loading="lazy" /></span>
        <span class="category-card__note">{{ tile.note }}</span>
        <strong>{{ tile.name }}</strong>
        <span class="round-arrow"><Icon name="arrow-up-right" :size="16" /></span>
      </button>
    </div>
  </section>
</template>
