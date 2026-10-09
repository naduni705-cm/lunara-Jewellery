<script setup>
import { onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const reducedMotion = ref(false)
onMounted(() => { reducedMotion.value = window.matchMedia('(prefers-reduced-motion: reduce)').matches })
</script>

<template>
  <section class="muse-editorial container reveal-on-scroll">
    <div class="muse-editorial__intro">
      <p class="eyebrow">The Lunara muse</p>
      <h2>Soft presence.<br /><em>Strong point of view.</em></h2>
      <p>We style pearls with restraint—one luminous detail, beautifully placed. The result feels modern, personal and entirely effortless.</p>
      <button class="text-link" type="button" @click="$emit('explore', 'Earrings')">Explore pearl earrings <Icon name="arrow-right" :size="16" /></button>
    </div>
    <div class="muse-editorial__portrait">
      <img :src="asset('images/lunara-reimagined-earrings.webp')" alt="Luminous pearls resting in a mother-of-pearl shell" loading="lazy" />
      <span class="muse-caption">Portrait study · No. 02</span>
    </div>
    <div class="muse-editorial__origin">
      <img :src="asset('images/be1e273369c0539e491b4596a0c11d9b-2.jpg')" alt="Luminous pearls resting in a mother-of-pearl shell" loading="lazy" />
      <div><span>Origin story</span><p>Born in water. Chosen by eye. Finished by hand.</p></div>
    </div>
  </section>
</template>
