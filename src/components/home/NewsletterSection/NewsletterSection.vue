<script setup>
import { ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

const email = ref('')
const sent = ref(false)
function submitNewsletter() {
  if (!email.value) return
  sent.value = true
  email.value = ''
}
</script>

<template>
  <section class="newsletter-section reveal-on-scroll">
    <div class="newsletter-pearl" aria-hidden="true"></div>
    <div>
      <p class="eyebrow">Private letters from Lunara</p>
      <h2>A little light,<br />delivered thoughtfully.</h2>
      <p>New editions, atelier stories and considered notes—shared occasionally.</p>
    </div>
    <form v-if="!sent" class="newsletter-form" @submit.prevent="submitNewsletter">
      <label class="sr-only" for="newsletter-email">Email address</label>
      <input id="newsletter-email" v-model="email" type="email" placeholder="Your email address" required />
      <button type="submit" aria-label="Join the Lunara mailing list"><Icon name="arrow-right" :size="18" /></button>
    </form>
    <div v-else class="form-success"><Icon name="check" :size="18" /> You are on the list. Welcome to Lunara.</div>
  </section>
</template>
