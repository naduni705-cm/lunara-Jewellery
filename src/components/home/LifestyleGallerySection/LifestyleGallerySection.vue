<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`

const moments = [
  {
    id: 'morning-light',
    number: '01',
    eyebrow: 'Everyday ritual',
    title: 'Morning light',
    category: 'Bracelets',
    image: 'images/lunara-marquee-01-wrist.webp',
    alt: 'Pearl bracelet and ring styled with an ivory silk sleeve',
    shape: 'arch',
    position: '50% 56%',
    lift: '20px',
  },
  {
    id: 'quiet-classic',
    number: '02',
    eyebrow: 'Portrait study',
    title: 'Quiet classic',
    category: 'Necklaces',
    image: 'images/lunara-marquee-02-portrait.webp',
    alt: 'Woman in champagne silk wearing pearl earrings and a pearl pendant',
    shape: 'soft',
    position: '50% 33%',
    lift: '0px',
  },
  {
    id: 'modern-forms',
    number: '03',
    eyebrow: 'The ring edit',
    title: 'Modern forms',
    category: 'Rings',
    image: 'images/lunara-marquee-03-rings.webp',
    alt: 'Hands wearing pearl rings against soft sage silk',
    shape: 'capsule',
    position: '50% 50%',
    lift: '28px',
  },
  {
    id: 'layered-lustre',
    number: '04',
    eyebrow: 'Softly layered',
    title: 'Layered lustre',
    category: 'Necklaces',
    image: 'images/lunara-marquee-04-necklace.webp',
    alt: 'Layered pearl strand and baroque pearl necklace styled with ivory silk',
    shape: 'reverse',
    position: '50% 42%',
    lift: '4px',
  },
  {
    id: 'sculpted-drop',
    number: '05',
    eyebrow: 'The new drop',
    title: 'Sculpted light',
    category: 'Earrings',
    image: 'images/lunara-marquee-05-earrings.webp',
    alt: 'Woman in sage silk wearing a sculptural graduated pearl earring',
    shape: 'arch',
    position: '50% 40%',
    lift: '24px',
  },
  {
    id: 'golden-stack',
    number: '06',
    eyebrow: 'City to coast',
    title: 'Golden stack',
    category: 'Bracelets',
    image: 'images/lunara-marquee-06-bracelets.webp',
    alt: 'Pearl bracelet and sculptural gold cuff styled with an ivory clutch',
    shape: 'soft',
    position: '50% 48%',
    lift: '0px',
  },
]

const section = ref(null)
const isPaused = ref(false)
const isVisible = ref(false)
let revealObserver

function togglePaused() {
  isPaused.value = !isPaused.value
}

onMounted(() => {
  if (!('IntersectionObserver' in window)) {
    isVisible.value = true
    return
  }

  revealObserver = new IntersectionObserver(
    ([entry]) => {
      if (!entry.isIntersecting) return
      isVisible.value = true
      revealObserver?.disconnect()
    },
    { threshold: 0.14 },
  )

  revealObserver.observe(section.value)
})

onBeforeUnmount(() => revealObserver?.disconnect())
</script>

<template>
  <section
    ref="section"
    class="lunara-marquee"
    :class="{ 'is-visible': isVisible, 'is-paused': isPaused }"
    aria-labelledby="lunara-marquee-title"
  >
    <span class="lunara-marquee__orb lunara-marquee__orb--one" aria-hidden="true"></span>
    <span class="lunara-marquee__orb lunara-marquee__orb--two" aria-hidden="true"></span>

    <header class="lunara-marquee__header">
      <div class="lunara-marquee__index" aria-hidden="true">
        <span>Journal</span>
        <strong>06</strong>
      </div>

      <div class="lunara-marquee__heading">
        <p><i></i> Worn in the world</p>
        <h2 id="lunara-marquee-title">As seen in <em>Lunara.</em></h2>
        <span>Six luminous ways to make pearls entirely your own.</span>
      </div>

      <button
        class="lunara-marquee__pause"
        type="button"
        :aria-label="isPaused ? 'Resume jewellery gallery' : 'Pause jewellery gallery'"
        :aria-pressed="isPaused"
        @click="togglePaused"
      >
        <span>{{ isPaused ? 'Play' : 'Pause' }}</span>
        <i><Icon :name="isPaused ? 'play' : 'pause'" :size="15" /></i>
      </button>
    </header>

    <div class="lunara-marquee__viewport" aria-label="Lunara jewellery style gallery">
      <div class="lunara-marquee__track">
        <div
          v-for="copyIndex in 2"
          :key="copyIndex"
          class="lunara-marquee__group"
          :class="{ 'lunara-marquee__group--duplicate': copyIndex === 2 }"
          :aria-hidden="copyIndex === 2 ? 'true' : undefined"
        >
          <button
            v-for="moment in moments"
            :key="`${copyIndex}-${moment.id}`"
            class="lunara-marquee__card"
            :class="`lunara-marquee__card--${moment.shape}`"
            :style="{ '--card-lift': moment.lift }"
            type="button"
            :tabindex="copyIndex === 2 ? -1 : 0"
            :aria-label="`Explore ${moment.title} ${moment.category.toLowerCase()}`"
            @click="$emit('explore', moment.category)"
          >
            <img
              :src="asset(moment.image)"
              :alt="copyIndex === 1 ? moment.alt : ''"
              :style="{ objectPosition: moment.position }"
              loading="lazy"
              decoding="async"
            />

            <span class="lunara-marquee__veil" aria-hidden="true"></span>
            <span class="lunara-marquee__number">{{ moment.number }}</span>

            <span class="lunara-marquee__card-copy">
              <small>{{ moment.eyebrow }}</small>
              <strong>{{ moment.title }}</strong>
              <span>{{ moment.category }}</span>
            </span>

            <span class="lunara-marquee__arrow">
              <Icon name="arrow-up-right" :size="17" />
            </span>
          </button>
        </div>
      </div>
    </div>

    <footer class="lunara-marquee__footer">
      <span>Natural pearls</span><i></i>
      <span>Small editions</span><i></i>
      <span>Designed for every day</span><i></i>
      <button type="button" @click="$emit('explore', 'All')">
        Shop the edit <Icon name="arrow-right" :size="16" />
      </button>
    </footer>
  </section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz,wght@6..96,400;6..96,500&family=Manrope:wght@400;500;600&display=swap');

.lunara-marquee {
  --lm-ivory: #fffaf2;
  --lm-cream: #f7eee2;
  --lm-blush: #efdcd8;
  --lm-sage: #aeb8a2;
  --lm-gold: #b58a54;
  --lm-taupe: #74655b;
  --lm-ink: #453a33;
  --lm-line: rgba(181, 138, 84, 0.42);
  --lm-gap: clamp(10px, 1vw, 18px);
  --lm-serif: 'Bodoni Moda', Didot, 'Bodoni MT', serif;
  --lm-sans: 'Manrope', 'Helvetica Neue', Arial, sans-serif;
  position: relative;
  isolation: isolate;
  overflow: hidden;
  padding: clamp(76px, 8vw, 130px) 0 clamp(48px, 5vw, 82px);
  color: var(--lm-ink);
  background:
    radial-gradient(circle at 14% 14%, rgba(239, 220, 216, 0.82), transparent 25%),
    radial-gradient(circle at 88% 88%, rgba(174, 184, 162, 0.28), transparent 24%),
    linear-gradient(180deg, #fffaf3 0%, #f6ede1 100%);
  border-top: 1px solid var(--lm-line);
  border-bottom: 1px solid var(--lm-line);
}

.lunara-marquee__orb {
  position: absolute;
  z-index: -1;
  display: block;
  border: 1px solid rgba(181, 138, 84, 0.18);
  border-radius: 50%;
  pointer-events: none;
}

.lunara-marquee__orb--one {
  top: -21vw;
  left: -14vw;
  width: 44vw;
  aspect-ratio: 1;
}

.lunara-marquee__orb--two {
  right: -12vw;
  bottom: -24vw;
  width: 48vw;
  aspect-ratio: 1;
}

.lunara-marquee__header {
  display: grid;
  grid-template-columns: minmax(110px, 0.65fr) minmax(0, 2fr) minmax(110px, 0.65fr);
  align-items: end;
  gap: clamp(24px, 4vw, 68px);
  width: min(91%, 1580px);
  margin: 0 auto clamp(44px, 5vw, 78px);
  opacity: 0;
  transform: translateY(22px);
  transition: opacity 0.85s ease 0.12s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.12s;
}

.lunara-marquee.is-visible .lunara-marquee__header {
  opacity: 1;
  transform: translateY(0);
}

.lunara-marquee__index {
  display: flex;
  align-items: center;
  gap: 12px;
  padding-bottom: 9px;
  font-family: var(--lm-sans);
  text-transform: uppercase;
}

.lunara-marquee__index span {
  color: rgba(69, 58, 51, 0.52);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.17em;
  writing-mode: vertical-rl;
}

.lunara-marquee__index strong {
  color: rgba(181, 138, 84, 0.54);
  font-family: var(--lm-serif);
  font-size: clamp(42px, 4vw, 68px);
  font-weight: 400;
  line-height: 1;
}

.lunara-marquee__heading {
  text-align: center;
}

.lunara-marquee__heading > p {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 11px;
  margin: 0;
  color: var(--lm-gold);
  font-family: var(--lm-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.lunara-marquee__heading > p i {
  width: 34px;
  height: 1px;
  background: currentColor;
}

.lunara-marquee__heading h2 {
  margin: 15px 0 13px;
  font-family: var(--lm-serif);
  font-size: clamp(53px, 6.5vw, 106px);
  font-weight: 400;
  line-height: 0.92;
  letter-spacing: -0.06em;
}

.lunara-marquee__heading h2 em {
  color: var(--lm-gold);
  font-weight: 400;
}

.lunara-marquee__heading > span {
  color: rgba(69, 58, 51, 0.62);
  font-family: var(--lm-sans);
  font-size: clamp(10px, 0.8vw, 13px);
  line-height: 1.6;
}

.lunara-marquee__pause {
  justify-self: end;
  display: inline-flex;
  align-items: center;
  gap: 11px;
  padding: 6px 6px 6px 15px;
  border: 1px solid var(--lm-line);
  border-radius: 999px;
  background: rgba(255, 250, 242, 0.52);
  box-shadow: 0 12px 30px rgba(91, 67, 50, 0.08);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  font-family: var(--lm-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.13em;
  text-transform: uppercase;
  cursor: pointer;
}

.lunara-marquee__pause i {
  display: grid;
  place-items: center;
  width: 31px;
  aspect-ratio: 1;
  border-radius: 50%;
  color: var(--lm-ivory);
  background: var(--lm-gold);
}

.lunara-marquee__viewport {
  position: relative;
  width: 100%;
  overflow: hidden;
  padding: 16px 0 48px;
  opacity: 0;
  transition: opacity 0.9s ease 0.35s;
}

.lunara-marquee.is-visible .lunara-marquee__viewport {
  opacity: 1;
}

.lunara-marquee__viewport::before,
.lunara-marquee__viewport::after {
  position: absolute;
  z-index: 8;
  top: 0;
  bottom: 0;
  width: clamp(35px, 7vw, 130px);
  content: '';
  pointer-events: none;
}

.lunara-marquee__viewport::before {
  left: 0;
  background: linear-gradient(90deg, #f9f1e7, transparent);
}

.lunara-marquee__viewport::after {
  right: 0;
  background: linear-gradient(270deg, #f5ecdf, transparent);
}

.lunara-marquee__track {
  display: flex;
  width: max-content;
  will-change: transform;
  animation: lunara-gallery-scroll 52s linear infinite;
}

.lunara-marquee.is-paused .lunara-marquee__track,
.lunara-marquee__viewport:hover .lunara-marquee__track,
.lunara-marquee__viewport:focus-within .lunara-marquee__track {
  animation-play-state: paused;
}

.lunara-marquee__group {
  display: flex;
  align-items: center;
  gap: var(--lm-gap);
  padding-right: var(--lm-gap);
}

.lunara-marquee__card {
  position: relative;
  width: clamp(220px, 18vw, 325px);
  height: clamp(330px, 28vw, 485px);
  flex: 0 0 auto;
  padding: 0;
  overflow: hidden;
  border: 1px solid rgba(181, 138, 84, 0.46);
  color: var(--lm-ivory);
  background: var(--lm-cream);
  box-shadow: 0 24px 58px rgba(89, 64, 47, 0.14);
  cursor: pointer;
  transform: translateY(var(--card-lift));
  transition: box-shadow 0.35s ease, transform 0.42s cubic-bezier(0.2, 0.75, 0.2, 1);
}

.lunara-marquee__card--arch {
  border-radius: 48% 48% 7px 7px / 19% 19% 7px 7px;
}

.lunara-marquee__card--soft {
  border-radius: 8px 76px 8px 8px;
}

.lunara-marquee__card--capsule {
  border-radius: 999px;
}

.lunara-marquee__card--reverse {
  border-radius: 76px 8px 8px 8px;
}

.lunara-marquee__card > img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.8s cubic-bezier(0.2, 0.7, 0.2, 1), filter 0.45s ease;
}

.lunara-marquee__veil {
  position: absolute;
  inset: 0;
  background:
    linear-gradient(180deg, rgba(52, 38, 30, 0.08) 36%, rgba(50, 36, 28, 0.66) 100%),
    linear-gradient(120deg, rgba(239, 220, 216, 0.08), transparent 55%);
  pointer-events: none;
}

.lunara-marquee__number {
  position: absolute;
  top: 18px;
  left: 18px;
  display: grid;
  place-items: center;
  width: 36px;
  aspect-ratio: 1;
  border: 1px solid rgba(255, 255, 255, 0.52);
  border-radius: 50%;
  background: rgba(255, 250, 242, 0.22);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  font-family: var(--lm-sans);
  font-size: 8px;
  font-weight: 600;
}

.lunara-marquee__card-copy {
  position: absolute;
  right: 14px;
  bottom: 14px;
  left: 14px;
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: end;
  gap: 4px 12px;
  padding: 17px 18px;
  border: 1px solid rgba(255, 255, 255, 0.48);
  border-radius: 4px 31px 4px 4px;
  background: rgba(86, 64, 51, 0.27);
  box-shadow: 0 16px 34px rgba(49, 34, 27, 0.13);
  backdrop-filter: blur(17px) saturate(112%);
  -webkit-backdrop-filter: blur(17px) saturate(112%);
  text-align: left;
}

.lunara-marquee__card-copy small {
  grid-column: 1 / -1;
  color: rgba(255, 250, 242, 0.74);
  font-family: var(--lm-sans);
  font-size: 7px;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.lunara-marquee__card-copy strong {
  font-family: var(--lm-serif);
  font-size: clamp(21px, 2vw, 31px);
  font-weight: 400;
  line-height: 1;
}

.lunara-marquee__card-copy > span {
  padding-bottom: 2px;
  font-family: var(--lm-sans);
  font-size: 7px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.lunara-marquee__arrow {
  position: absolute;
  top: 18px;
  right: 18px;
  display: grid;
  place-items: center;
  width: 40px;
  aspect-ratio: 1;
  border: 1px solid rgba(255, 255, 255, 0.6);
  border-radius: 50%;
  color: var(--lm-taupe);
  background: rgba(255, 250, 242, 0.7);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  transition: color 0.28s ease, background 0.28s ease, transform 0.28s ease;
}

.lunara-marquee__card:hover,
.lunara-marquee__card:focus-visible {
  box-shadow: 0 34px 72px rgba(89, 64, 47, 0.2);
  transform: translateY(calc(var(--card-lift) - 9px));
}

.lunara-marquee__card:hover > img,
.lunara-marquee__card:focus-visible > img {
  filter: saturate(1.03);
  transform: scale(1.045);
}

.lunara-marquee__card:hover .lunara-marquee__arrow,
.lunara-marquee__card:focus-visible .lunara-marquee__arrow {
  color: var(--lm-gold);
  background: rgba(255, 250, 242, 0.92);
  transform: translate(3px, -3px);
}

.lunara-marquee__footer {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: clamp(12px, 2vw, 30px);
  width: min(91%, 1580px);
  margin: clamp(24px, 3vw, 45px) auto 0;
  padding-top: 25px;
  border-top: 1px solid var(--lm-line);
  color: rgba(69, 58, 51, 0.58);
  font-family: var(--lm-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.14em;
  text-transform: uppercase;
}

.lunara-marquee__footer > i {
  width: 4px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--lm-gold);
}

.lunara-marquee__footer button {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  margin-left: auto;
  padding: 0 0 7px;
  border: 0;
  border-bottom: 1px solid rgba(181, 138, 84, 0.7);
  background: transparent;
  font: inherit;
  cursor: pointer;
  transition: color 0.25s ease, gap 0.25s ease;
}

.lunara-marquee__footer button:hover {
  gap: 15px;
  color: var(--lm-gold);
}

.lunara-marquee button:focus-visible {
  outline: 2px solid var(--lm-gold);
  outline-offset: 4px;
}

@keyframes lunara-gallery-scroll {
  from { transform: translate3d(0, 0, 0); }
  to { transform: translate3d(-50%, 0, 0); }
}

@media (max-width: 760px) {
  .lunara-marquee {
    padding-top: 72px;
  }

  .lunara-marquee__header {
    grid-template-columns: 1fr auto;
    align-items: center;
  }

  .lunara-marquee__index {
    display: none;
  }

  .lunara-marquee__heading {
    text-align: left;
  }

  .lunara-marquee__heading > p {
    justify-content: flex-start;
  }

  .lunara-marquee__heading h2 {
    font-size: clamp(48px, 11vw, 76px);
  }

  .lunara-marquee__pause > span {
    display: none;
  }

  .lunara-marquee__pause {
    padding: 5px;
  }

  .lunara-marquee__card {
    width: clamp(220px, 62vw, 290px);
    height: clamp(335px, 88vw, 430px);
  }

  .lunara-marquee__footer {
    justify-content: flex-start;
    overflow: hidden;
  }

  .lunara-marquee__footer > span:nth-of-type(3),
  .lunara-marquee__footer > i:nth-of-type(3) {
    display: none;
  }
}

@media (max-width: 520px) {
  .lunara-marquee__header {
    width: calc(100% - 36px);
    margin-bottom: 34px;
  }

  .lunara-marquee__heading h2 {
    font-size: 47px;
  }

  .lunara-marquee__heading > span {
    display: block;
    max-width: 260px;
  }

  .lunara-marquee__viewport {
    padding-bottom: 38px;
  }

  .lunara-marquee__footer {
    width: calc(100% - 36px);
    gap: 10px;
  }

  .lunara-marquee__footer > span:nth-of-type(2),
  .lunara-marquee__footer > i:nth-of-type(2) {
    display: none;
  }

  .lunara-marquee__footer button {
    margin-left: auto;
  }
}

@media (prefers-reduced-motion: reduce) {
  .lunara-marquee *,
  .lunara-marquee *::before,
  .lunara-marquee *::after {
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
    transition-delay: 0ms !important;
  }

  .lunara-marquee__viewport {
    overflow-x: auto;
    scrollbar-width: thin;
  }

  .lunara-marquee__track {
    animation: none !important;
  }

  .lunara-marquee__group--duplicate {
    display: none;
  }
}
</style>
