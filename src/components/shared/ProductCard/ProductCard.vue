<script setup>
import Icon from '../Icon/Icon.vue'

defineProps({
  product: { type: Object, required: true },
  wished: { type: Boolean, default: false },
})

defineEmits(['add', 'wish', 'open'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const money = (value) => new Intl.NumberFormat('en-US', { style: 'currency', currency: 'USD', maximumFractionDigits: 0 }).format(value)
</script>

<template>
  <article class="product-card">
    <div class="product-card__media" @click="$emit('open', product)" role="button" tabindex="0" @keydown.enter="$emit('open', product)">
      <img :src="asset(product.image)" :alt="product.name" loading="lazy" />
      <span v-if="product.badge" class="product-card__badge">{{ product.badge }}</span>
      <button
        class="product-card__wish"
        :class="{ 'is-active': wished }"
        type="button"
        :aria-label="wished ? `Remove ${product.name} from wishlist` : `Add ${product.name} to wishlist`"
        @click.stop="$emit('wish', product)"
      >
        <Icon name="heart" :size="18" />
      </button>
      <button class="product-card__quick" type="button" @click.stop="$emit('open', product)">
        Quick view
      </button>
    </div>
    <div class="product-card__body">
      <button class="product-card__title" type="button" @click="$emit('open', product)">{{ product.name }}</button>
      <span class="product-card__price">{{ money(product.price) }}</span>
      <p>{{ product.category }} · {{ product.pearl }}</p>
      <button class="text-link product-card__add" type="button" @click="$emit('add', product)">
        Add to bag <Icon name="plus" :size="15" />
      </button>
    </div>
  </article>
</template>
