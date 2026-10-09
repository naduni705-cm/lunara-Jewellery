<script setup>
import { computed, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'
import { products } from '../../../data/catalog.js'

defineEmits(['open-product'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const finderChoice = ref(0)
const finderOptions = [
  { label: 'Timeless', title: 'The eternal icon', copy: 'Balanced, luminous and effortless—the pearl strand that belongs with everything.', productId: 'classic-moon-strand' },
  { label: 'Sculptural', title: 'A modern point of view', copy: 'Quietly architectural gold and pearls for a look that feels distinctly now.', productId: 'selene-twin-pearl-cuff' },
  { label: 'Organic', title: 'Beautifully one of one', copy: 'An expressive natural form chosen for character, soft asymmetry and individual light.', productId: 'luminous-baroque-pendant' },
]
const finderResult = computed(() => {
  const option = finderOptions[finderChoice.value]
  return { ...option, product: products.find((product) => product.id === option.productId) }
})
</script>

<template>
  <section class="pearl-finder reveal-on-scroll">
    <div class="pearl-finder__intro">
      <p class="eyebrow">Find your pearl</p>
      <h2>What feels<br /><em>most like you?</em></h2>
      <p>Choose a mood and we will lead you to a piece with the right kind of presence.</p>
      <div class="finder-tabs" role="tablist" aria-label="Choose your jewellery mood">
        <button v-for="(option, index) in finderOptions" :key="option.label" type="button" role="tab" :aria-selected="finderChoice === index" :class="{ active: finderChoice === index }" @click="finderChoice = index">
          <span>{{ String(index + 1).padStart(2, '0') }}</span>{{ option.label }}
        </button>
      </div>
    </div>
    <Transition name="finder" mode="out-in">
      <div :key="finderResult.productId" class="pearl-finder__result">
        <div class="finder-product-image"><img :src="asset(finderResult.product.image)" :alt="finderResult.product.name" /></div>
        <div class="finder-result-copy">
          <p class="eyebrow">Your Lunara match</p>
          <h3>{{ finderResult.title }}</h3>
          <p>{{ finderResult.copy }}</p>
          <button class="text-link" type="button" @click="$emit('open-product', finderResult.product)">Meet {{ finderResult.product.name }} <Icon name="arrow-right" :size="16" /></button>
        </div>
      </div>
    </Transition>
  </section>
</template>
