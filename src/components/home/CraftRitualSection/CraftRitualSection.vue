<script setup>
import Icon from '../../shared/Icon/Icon.vue'
</script>

<template>
  <section class="ritual-section reveal-on-scroll">
    <div class="section-heading section-heading--center">
      <p class="eyebrow">The Lunara ritual</p>
      <h2>Considered at every step</h2>
    </div>
    <div class="ritual-grid container">
      <article><span>01</span><div class="ritual-icon"><Icon name="sparkle" :size="25" /></div><h3>Chosen by eye</h3><p>Pearls are matched for lustre, tone and a natural rhythm that feels balanced.</p></article>
      <article><span>02</span><div class="ritual-icon"><span class="tiny-pearl"></span></div><h3>Finished by hand</h3><p>Each piece is knotted, polished and closely checked in a small atelier edition.</p></article>
      <article><span>03</span><div class="ritual-icon"><Icon name="shield" :size="25" /></div><h3>Cared for, always</h3><p>Our two-year promise and lifetime repair service help your jewellery travel further.</p></article>
    </div>
  </section>
</template>
