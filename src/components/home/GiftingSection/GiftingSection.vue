<script setup>
import Icon from '../../shared/Icon/Icon.vue'
defineEmits(['explore'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
</script>

<template>
  <section class="gifting-section container reveal-on-scroll">
    <div class="gifting-section__image">
      <img :src="asset('images/lunara-reimagined-portrait.webp')" alt="Pearl bracelet and earrings presented in an ivory jewellery box" loading="lazy" />
      <span class="gift-ribbon-label">Wrapped by hand</span>
    </div>
    <div class="gifting-section__copy">
      <p class="eyebrow">The art of giving</p>
      <h2>A beautiful moment,<br /><em>before it is opened.</em></h2>
      <p>Every Lunara piece arrives in our signature keepsake box, wrapped in soft sage ribbon and ready to become part of someone’s story.</p>
      <ul>
        <li><Icon name="check" :size="15" /> Complimentary signature wrapping</li>
        <li><Icon name="check" :size="15" /> Handwritten message on request</li>
        <li><Icon name="check" :size="15" /> Discreet worldwide delivery</li>
      </ul>
      <button class="button button--outline" type="button" @click="$emit('explore', 'All')">Choose a gift <Icon name="arrow-right" :size="16" /></button>
    </div>
  </section>
</template>
