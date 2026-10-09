<script setup>
import Icon from '../../shared/Icon/Icon.vue'
defineEmits(['navigate'])
</script>

<template>
  <section class="concierge-section reveal-on-scroll">
    <div class="concierge-mark"><span class="tiny-pearl"></span><span class="tiny-pearl"></span><span class="tiny-pearl"></span></div>
    <div><p class="eyebrow">Private boutique service</p><h2>Need a little guidance?</h2><p>Meet with a Lunara stylist online for gifting, sizing or a more personal pearl recommendation.</p></div>
    <button class="button button--ivory" type="button" @click="$emit('navigate', '/contact')">Book a private appointment <Icon name="arrow-right" :size="17" /></button>
  </section>
</template>
