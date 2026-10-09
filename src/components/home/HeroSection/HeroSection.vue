<script setup>
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore', 'navigate'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
</script>

<template>
  <section class="editorial-hero" aria-labelledby="hero-title">
    <div class="editorial-hero__media" aria-hidden="true">
      <img :src="asset('images/hero-pearl-editorial.webp')" alt="" />
    </div>
    <div class="editorial-hero__veil" aria-hidden="true"></div>

    <div class="editorial-hero__content">
      <div class="editorial-hero__copy">
        <p class="editorial-hero__eyebrow">The modern pearl edit</p>
        <h1 id="hero-title">A quiet ritual<br /><em>of radiance.</em></h1>
        <p class="editorial-hero__intro">Luminous freshwater pearls, selected by eye and hand-finished in small editions.</p>
        <button class="editorial-hero__cta" type="button" @click="$emit('explore', 'All')">
          Discover collection <Icon name="arrow-right" :size="17" />
        </button>
      </div>

      <div class="editorial-hero__destinations" aria-label="Explore Lunara">
        <button class="editorial-destination" type="button" @click="$emit('explore', 'All')">
          <span class="editorial-destination__media" aria-hidden="true"><img :src="asset('images/luna-studs.webp')" alt="" /></span>
          <span class="editorial-destination__copy"><strong>Shop</strong><small>Curated essentials</small><Icon name="arrow-up-right" :size="16" /></span>
        </button>
        <button class="editorial-destination" type="button" @click="$emit('explore', 'Necklaces')">
          <span class="editorial-destination__media" aria-hidden="true"><img :src="asset('images/organic-strand.webp')" alt="" /></span>
          <span class="editorial-destination__copy"><strong>Necklaces</strong><small>Signature strands</small><Icon name="arrow-up-right" :size="16" /></span>
        </button>
        <button class="editorial-destination" type="button" @click="$emit('explore', 'Earrings')">
          <span class="editorial-destination__media" aria-hidden="true"><img :src="asset('images/celeste-drops.webp')" alt="" /></span>
          <span class="editorial-destination__copy"><strong>Earrings</strong><small>Everyday light</small><Icon name="arrow-up-right" :size="16" /></span>
        </button>
        <button class="editorial-destination" type="button" @click="$emit('navigate', '/journal')">
          <span class="editorial-destination__media" aria-hidden="true"><img :src="asset('images/atelier-craft.webp')" alt="" /></span>
          <span class="editorial-destination__copy"><strong>Journal</strong><small>Pearl stories</small><Icon name="arrow-up-right" :size="16" /></span>
        </button>
      </div>
    </div>
  </section>
</template>
