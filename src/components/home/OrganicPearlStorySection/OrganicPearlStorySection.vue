<script setup>
import Icon from '../../shared/Icon/Icon.vue'
defineEmits(['navigate'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
</script>

<template>
  <section class="editorial-split reveal-on-scroll">
    <div class="editorial-split__image">
      <img :src="asset('images/pearl-origin.webp')" alt="Baroque pearl pendant on a fine gold chain" loading="lazy" />
      <span class="floating-seal">Naturally<br />one of one</span>
    </div>
    <div class="editorial-split__copy">
      <p class="eyebrow">The new heirloom</p>.  
      <h2>Beautifully <em>individual.</em></h2>
      <p>No two pearls carry light in quite the same way. We celebrate their natural ridges, soft tones and unexpected silhouettes—then frame each one with quiet, thoughtful design.</p>
      <button class="text-link" type="button" @click="$emit('navigate', '/collections')">Explore organic forms <Icon name="arrow-right" :size="16" /></button>
      <dl class="editorial-stats">
        <div><dt>Small editions</dt><dd>Made slowly, never over-produced</dd></div>
        <div><dt>Natural character</dt><dd>Every pearl is distinctly yours</dd></div>
      </dl>
    </div>
  </section>
</template>
