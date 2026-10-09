<script setup>
import Icon from '../../shared/Icon/Icon.vue'
defineProps({ articles: { type: Array, required: true } })
defineEmits(['navigate'])
const asset = (path) => `${import.meta.env.BASE_URL}${path}`
</script>

<template>
  <section class="section journal-preview container reveal-on-scroll">
    <div class="section-heading section-heading--row">
      <div><p class="eyebrow">The journal</p><h2>Notes on quiet beauty</h2></div>
      <button class="text-link" type="button" @click="$emit('navigate', '/journal')">Read all stories <Icon name="arrow-right" :size="16" /></button>
    </div>
    <div class="journal-grid">
      <article v-for="article in articles" :key="article.title" class="journal-card">
        <div class="journal-card__image"><img :src="asset(article.image)" :alt="article.title" loading="lazy" /></div>
        <p class="eyebrow">{{ article.tag }}</p>
        <h3>{{ article.title }}</h3>
        <div class="journal-card__meta"><span>{{ article.date }}</span><span>{{ article.read }}</span></div>
      </article>
    </div>
  </section>
</template>
