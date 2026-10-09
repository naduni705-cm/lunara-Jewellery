<script setup>
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`

const campaigns = [
  {
    id: 'pearl-drops',
    number: '01',
    eyebrow: 'The new essential',
    title: 'Pearl drop icons',
    copy: 'Natural lustre, framed in warm gold and designed to move beautifully with you.',
    action: 'Shop earrings',
    category: 'Earrings',
    image: 'images/7cbaa963aeaedd5bd94a4621c3a8c629.jpg',
    alt: 'Model wearing a freshwater pearl drop earring in a warm gold setting',
    position: '50% 38%',
  },
  {
    id: 'golden-layers',
    number: '02',
    eyebrow: 'Quietly luminous',
    title: 'Golden layers',
    copy: 'Fine champagne-gold chains and a single pearl, balanced for effortless everyday styling.',
    action: 'Shop necklaces',
    category: 'Necklaces',
    image: 'images/lunara-work-05-pearl-necklace.webp',
    alt: 'Model in ivory silk wearing layered gold necklaces with a pearl pendant',
    position: '50% 30%',
  },
]
</script>

<template>
  <section class="editorial-pair" aria-labelledby="editorial-pair-title">
    <h2 id="editorial-pair-title" class="sr-only">The Lunara jewellery campaign</h2>

    <div class="editorial-pair__grid">
      <article
        v-for="campaign in campaigns"
        :key="campaign.id"
        class="campaign-panel"
      >
        <img
          :src="asset(campaign.image)"
          :alt="campaign.alt"
          :style="{ objectPosition: campaign.position }"
          loading="lazy"
        />

        <span class="campaign-panel__veil" aria-hidden="true"></span>
        <span class="campaign-panel__glow" aria-hidden="true"></span>

        <header class="campaign-panel__header">
          <span>Lunara</span>
          <span>{{ campaign.number }} / {{ String(campaigns.length).padStart(2, '0') }}</span>
        </header>

        <div class="campaign-panel__copy">
          <p>{{ campaign.eyebrow }}</p>
          <h3>{{ campaign.title }}</h3>
          <span class="campaign-panel__description">{{ campaign.copy }}</span>

          <button
            class="campaign-panel__action"
            type="button"
            @click="$emit('explore', campaign.category)"
          >
            {{ campaign.action }}
            <span><Icon name="arrow-up-right" :size="18" /></span>
          </button>
        </div>

        <span class="campaign-panel__index" aria-hidden="true">{{ campaign.number }}</span>
      </article>
    </div>

    <div class="editorial-pair__seal" aria-hidden="true">
      <span class="seal-pearl"></span>
      <small>Two ways<br />to glow</small>
    </div>
  </section>
</template>

<style scoped>
.editorial-pair {
  position: relative;
  padding: clamp(5px, 0.55vw, 10px);
  overflow: hidden;
  color: #fffaf3;
  background: #f6f1e8;
}

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}

.editorial-pair__grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: clamp(3px, 0.35vw, 7px);
  min-height: clamp(580px, 78svh, 860px);
}

.campaign-panel {
  position: relative;
  min-width: 0;
  min-height: inherit;
  overflow: hidden;
  isolation: isolate;
  background: #4c3428;
}

.campaign-panel > img {
  position: absolute;
  inset: 0;
  z-index: -3;
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  filter: saturate(0.88) contrast(1.02);
  transform: scale(1.015);
  transition:
    transform 1.1s cubic-bezier(0.2, 0.7, 0.2, 1),
    filter 700ms ease;
}

.campaign-panel:hover > img,
.campaign-panel:focus-within > img {
  filter: saturate(1) contrast(1.035);
  transform: scale(1.055);
}

.campaign-panel__veil,
.campaign-panel__glow {
  position: absolute;
  inset: 0;
  z-index: -2;
  pointer-events: none;
}

.campaign-panel__veil {
  background:
    linear-gradient(180deg, rgb(22 15 11 / 0.32) 0%, transparent 28%),
    linear-gradient(0deg, rgb(20 13 10 / 0.82) 0%, rgb(20 13 10 / 0.3) 35%, transparent 66%);
}

.campaign-panel__glow {
  z-index: -1;
  background: radial-gradient(circle at 12% 84%, rgb(220 183 127 / 0.2), transparent 28%);
  mix-blend-mode: screen;
}

.campaign-panel__header {
  position: absolute;
  top: clamp(22px, 3vw, 46px);
  right: clamp(22px, 3.6vw, 58px);
  left: clamp(22px, 3.6vw, 58px);
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  padding-bottom: 13px;
  border-bottom: 1px solid rgb(255 250 243 / 0.42);
  font: 650 0.62rem / 1 var(--font-sans, 'Helvetica Neue', Arial, sans-serif);
  letter-spacing: 0.24em;
  text-transform: uppercase;
}

.campaign-panel__header span:first-child {
  font-family: var(--font-display, 'Cormorant Garamond', Georgia, serif);
  font-size: 0.85rem;
  font-weight: 500;
  letter-spacing: 0.34em;
}

.campaign-panel__copy {
  position: absolute;
  right: clamp(24px, 4vw, 66px);
  bottom: clamp(30px, 5vw, 72px);
  left: clamp(24px, 4vw, 66px);
  max-width: 40rem;
}

.campaign-panel__copy > p {
  margin: 0 0 13px;
  font: 700 0.63rem / 1 var(--font-sans, 'Helvetica Neue', Arial, sans-serif);
  letter-spacing: 0.22em;
  text-transform: uppercase;
  opacity: 0.86;
}

.campaign-panel__copy h3 {
  max-width: 10ch;
  margin: 0;
  font: 400 clamp(2.7rem, 5vw, 6.4rem) / 0.86 var(--font-display, 'Cormorant Garamond', Georgia, serif);
  letter-spacing: -0.045em;
  text-wrap: balance;
}

.campaign-panel__description {
  display: block;
  max-width: 31rem;
  margin-top: clamp(15px, 2vw, 24px);
  font: 400 clamp(0.82rem, 0.95vw, 0.96rem) / 1.65 var(--font-sans, 'Helvetica Neue', Arial, sans-serif);
  opacity: 0.82;
}

.campaign-panel__action {
  display: inline-flex;
  align-items: center;
  gap: 14px;
  margin-top: clamp(20px, 2.5vw, 32px);
  padding: 0;
  color: inherit;
  border: 0;
  background: transparent;
  font: 700 0.66rem / 1 var(--font-sans, 'Helvetica Neue', Arial, sans-serif);
  letter-spacing: 0.17em;
  text-transform: uppercase;
  cursor: pointer;
}

.campaign-panel__action > span {
  display: grid;
  width: 44px;
  aspect-ratio: 1;
  place-items: center;
  color: #2d251f;
  border-radius: 50%;
  background: #fffaf3;
  transition: color 250ms ease, background-color 250ms ease, transform 250ms ease;
}

.campaign-panel__action:hover > span,
.campaign-panel__action:focus-visible > span {
  color: #fffaf3;
  background: #a8885e;
  transform: translate(3px, -3px);
}

.campaign-panel__action:focus-visible {
  outline: 2px solid #fffaf3;
  outline-offset: 7px;
}

.campaign-panel__index {
  position: absolute;
  right: clamp(20px, 3vw, 48px);
  bottom: clamp(24px, 3.5vw, 54px);
  font: 400 clamp(3rem, 6vw, 7.5rem) / 0.75 var(--font-display, 'Cormorant Garamond', Georgia, serif);
  opacity: 0.12;
  pointer-events: none;
}

.editorial-pair__seal {
  position: absolute;
  top: 50%;
  left: 50%;
  z-index: 3;
  display: grid;
  width: clamp(86px, 8vw, 118px);
  aspect-ratio: 1;
  place-items: center;
  color: #3e332b;
  border: 1px solid rgb(104 85 65 / 0.3);
  border-radius: 50%;
  background: rgb(250 246 239 / 0.94);
  box-shadow: 0 18px 55px rgb(28 18 12 / 0.18);
  transform: translate(-50%, -50%);
  backdrop-filter: blur(10px);
}

.editorial-pair__seal small {
  font: 700 0.55rem / 1.35 var(--font-sans, 'Helvetica Neue', Arial, sans-serif);
  letter-spacing: 0.14em;
  text-align: center;
  text-transform: uppercase;
}

.seal-pearl {
  position: absolute;
  top: 17%;
  width: 9px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: radial-gradient(circle at 35% 30%, #fff, #e9dfd0 54%, #bcae99);
  box-shadow: 0 2px 7px rgb(71 55 38 / 0.2);
}

@media (max-width: 900px) {
  .editorial-pair__grid {
    min-height: clamp(520px, 72svh, 720px);
  }

  .campaign-panel__copy h3 {
    font-size: clamp(2.6rem, 7vw, 4.8rem);
  }

  .campaign-panel__description {
    max-width: 25rem;
  }

  .campaign-panel__index {
    display: none;
  }
}

@media (max-width: 720px) {
  .editorial-pair {
    padding: 5px;
  }

  .editorial-pair__grid {
    grid-template-columns: 1fr;
    gap: 5px;
    min-height: 0;
  }

  .campaign-panel {
    min-height: clamp(540px, 78svh, 720px);
  }

  .campaign-panel__header {
    top: 22px;
    right: 22px;
    left: 22px;
  }

  .campaign-panel__copy {
    right: 24px;
    bottom: 30px;
    left: 24px;
  }

  .campaign-panel__copy h3 {
    max-width: 9ch;
    font-size: clamp(3.1rem, 14vw, 5.1rem);
  }

  .campaign-panel__description {
    max-width: 29rem;
    font-size: 0.86rem;
  }

  .editorial-pair__seal {
    top: 50%;
    width: 88px;
  }
}

@media (max-width: 430px) {
  .campaign-panel {
    min-height: 620px;
  }

  .campaign-panel__copy h3 {
    font-size: clamp(3rem, 17vw, 4.4rem);
  }

  .campaign-panel__description {
    display: none;
  }
}

@media (prefers-reduced-motion: reduce) {
  .campaign-panel > img,
  .campaign-panel__action > span {
    transition: none;
  }
}
</style>
