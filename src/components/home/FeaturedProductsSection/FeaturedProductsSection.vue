<script setup>
import Icon from '../../shared/Icon/Icon.vue'
import ProductCard from '../../shared/ProductCard/ProductCard.vue'

defineProps({
  products: { type: Array, required: true },
  wishlist: { type: Array, default: () => [] },
})
defineEmits(['explore', 'add', 'wish', 'open'])
</script>

<template>
  <section class="section container reveal-on-scroll">
    <div class="section-heading section-heading--row">
      <div>
        <p class="eyebrow">Most loved</p>
        <h2>Signature pieces</h2>
      </div>
      <button class="text-link" type="button" @click="$emit('explore', 'All')">Shop all jewellery <Icon name="arrow-right" :size="16" /></button>
    </div>
    <div class="product-grid">
      <ProductCard
        v-for="product in products"
        :key="product.id"
        :product="product"
        :wished="wishlist.includes(product.id)"
        @add="$emit('add', $event)"
        @wish="$emit('wish', $event)"
        @open="$emit('open', $event)"
      />
    </div>
  </section>
</template>
