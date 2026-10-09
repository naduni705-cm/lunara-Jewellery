<script setup>
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`

const steps = [
  {
    label: 'Source',
    eyebrow: 'Origin · 01',
    title: 'Chosen by eye',
    copy: 'Each pearl is selected for lustre, surface character and the quiet warmth it brings to the skin.',
  },
  {
    label: 'Sort',
    eyebrow: 'Grading · 02',
    title: 'Matched by light',
    copy: 'Pearls are slowly paired by tone, scale and reflection until the composition feels naturally balanced.',
  },
  {
    label: 'Sketch',
    eyebrow: 'Drawing · 03',
    title: 'Drawn with restraint',
    copy: 'Every silhouette begins as a hand sketch, reduced until only the most graceful line remains.',
  },
  {
    label: 'Model',
    eyebrow: 'Form · 04',
    title: 'Balanced in form',
    copy: 'Digital and hand-built models refine proportion, movement and comfort before precious metal is cast.',
  },
  {
    label: 'Cast',
    eyebrow: 'Metal · 05',
    title: 'Formed in gold',
    copy: 'Warm champagne gold is cast in small runs, preserving the clarity and delicacy of the original design.',
  },
  {
    label: 'Set',
    eyebrow: 'Setting · 06',
    title: 'Placed by hand',
    copy: 'Every pearl and stone is individually secured by an artisan using precise, almost invisible settings.',
  },
  {
    label: 'Polish',
    eyebrow: 'Lustre · 07',
    title: 'Brought to light',
    copy: 'Edges are softened and each surface is polished by hand for a glow that complements natural nacre.',
  },
  {
    label: 'Finish',
    eyebrow: 'Final care · 08',
    title: 'Checked, then yours',
    copy: 'The completed piece is inspected from every angle, wrapped in soft materials and prepared to travel.',
  },
]

const section = ref(null)
const activeIndex = ref(0)
const isVisible = ref(false)
const prefersReducedMotion = ref(false)
let revealObserver
let rotationTimer

const activeStep = computed(() => steps[activeIndex.value])
const dialStyle = computed(() => ({
  '--dial-angle': `${activeIndex.value * 45 - 90}deg`,
  '--dial-short-angle': `${activeIndex.value * 45 + 28}deg`,
}))

const markerStyle = (index) => {
  const angle = index * 45 - 90
  return {
    '--marker-angle': `${angle}deg`,
    '--marker-counter-angle': `${-angle}deg`,
  }
}

function pauseRotation() {
  window.clearInterval(rotationTimer)
}

function startRotation() {
  pauseRotation()
  if (prefersReducedMotion.value) return
  rotationTimer = window.setInterval(() => {
    activeIndex.value = (activeIndex.value + 1) % steps.length
  }, 4200)
}

function selectStep(index) {
  activeIndex.value = (index + steps.length) % steps.length
  startRotation()
}

function previousStep() {
  selectStep(activeIndex.value - 1)
}

function nextStep() {
  selectStep(activeIndex.value + 1)
}

onMounted(() => {
  prefersReducedMotion.value = window.matchMedia('(prefers-reduced-motion: reduce)').matches

  if (!('IntersectionObserver' in window)) {
    isVisible.value = true
    startRotation()
    return
  }

  revealObserver = new IntersectionObserver(
    ([entry]) => {
      if (!entry.isIntersecting) return
      isVisible.value = true
      startRotation()
      revealObserver?.disconnect()
    },
    { threshold: 0.12 },
  )

  revealObserver.observe(section.value)
})

onBeforeUnmount(() => {
  pauseRotation()
  revealObserver?.disconnect()
})
</script>

<template>
  <section
    ref="section"
    class="atelier-craft"
    :class="{ 'is-visible': isVisible }"
    aria-labelledby="atelier-craft-title"
  >
    <div
      class="atelier-craft__frame"
      tabindex="0"
      @mouseenter="pauseRotation"
      @mouseleave="startRotation"
      @focusin="pauseRotation"
      @focusout="startRotation"
      @keydown.left.prevent="previousStep"
      @keydown.right.prevent="nextStep"
    >
      <header class="atelier-craft__header">
        <p class="atelier-craft__brand">
          <strong>Lunara</strong>
          <span>The pearl atelier</span>
        </p>

        <p class="atelier-craft__header-note">Eight measured stages · One enduring piece</p>

        <div class="atelier-craft__header-links" aria-hidden="true">
          <span>Craft</span><i></i><span>Materials</span><i></i><span>Care</span>
        </div>
      </header>

      <div class="atelier-craft__body">
        <div class="atelier-craft__wheel-column">
          <div class="atelier-craft__wheel">
            <img
              :src="asset('images/lunara-atelier-wheel.webp')"
              alt="Eight stages of pearl jewellery making, from pearl selection and sketching to casting and polishing"
              loading="lazy"
              decoding="async"
            />

            <div class="atelier-craft__dial" :style="dialStyle">
              <span class="atelier-craft__ticks" aria-hidden="true"></span>

              <button
                v-for="(step, index) in steps"
                :key="step.label"
                class="atelier-craft__marker"
                :class="{ 'is-active': activeIndex === index }"
                :style="markerStyle(index)"
                type="button"
                :aria-label="`View step ${index + 1}: ${step.title}`"
                :aria-pressed="activeIndex === index"
                @click="selectStep(index)"
              >
                <span>{{ step.label }}</span>
              </button>

              <span class="atelier-craft__hand atelier-craft__hand--long" aria-hidden="true"></span>
              <span class="atelier-craft__hand atelier-craft__hand--short" aria-hidden="true"></span>
              <span class="atelier-craft__pin" aria-hidden="true"></span>

              <div class="atelier-craft__readout" aria-live="polite">
                <small>{{ activeStep.eyebrow }}</small>
                <strong>{{ activeStep.title }}</strong>
              </div>
            </div>
          </div>

          <div class="atelier-craft__wheel-caption">
            <span>{{ String(activeIndex + 1).padStart(2, '0') }}</span>
            <i></i>
            <p>From water to heirloom<br /><strong>Made slowly, finished closely.</strong></p>
          </div>
        </div>

        <div class="atelier-craft__content">
          <div class="atelier-craft__copy">
            <p class="atelier-craft__eyebrow"><i></i> Inside the atelier</p>
            <h2 id="atelier-craft-title">Crafted slowly.<br /><em>Beautiful forever.</em></h2>
            <p class="atelier-craft__intro">
              Fine jewellery asks for time. From the first pearl selected to the final polish,
              every Lunara piece passes through patient, expert hands.
            </p>
          </div>

          <article class="atelier-craft__active-step">
            <span>{{ String(activeIndex + 1).padStart(2, '0') }}</span>
            <div>
              <small>{{ activeStep.eyebrow }}</small>
              <h3>{{ activeStep.title }}</h3>
              <p>{{ activeStep.copy }}</p>
            </div>
          </article>

          <div class="atelier-craft__controls">
            <div>
              <button type="button" aria-label="Previous craftsmanship stage" @click="previousStep">
                <Icon class="atelier-craft__back" name="arrow-right" :size="18" />
              </button>
              <button type="button" aria-label="Next craftsmanship stage" @click="nextStep">
                <Icon name="arrow-right" :size="18" />
              </button>
            </div>

            <button class="atelier-craft__discover" type="button" @click="$emit('explore', 'All')">
              Discover our pieces <Icon name="arrow-up-right" :size="17" />
            </button>
          </div>

          <figure class="atelier-craft__maker-card">
            <img
              :src="asset('images/lunara-atelier-hands.webp')"
              alt="Jewellery artisan hand-knotting a freshwater pearl strand"
              loading="lazy"
              decoding="async"
            />
            <span class="atelier-craft__maker-veil" aria-hidden="true"></span>
            <figcaption>
              <small>Atelier note · 08</small>
              <strong>The human touch.</strong>
              <span>Hand-knotted, hand-finished, individually checked.</span>
            </figcaption>
          </figure>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz,wght@6..96,400;6..96,500&family=Manrope:wght@400;500;600&display=swap');

.atelier-craft {
  --ac-ivory: #fffaf2;
  --ac-cream: #f7eee2;
  --ac-blush: #efdcd8;
  --ac-sage: #aeb8a2;
  --ac-gold: #b58a54;
  --ac-taupe: #74655b;
  --ac-ink: #453a33;
  --ac-line: rgba(181, 138, 84, 0.42);
  --ac-serif: 'Bodoni Moda', Didot, 'Bodoni MT', serif;
  --ac-sans: 'Manrope', 'Helvetica Neue', Arial, sans-serif;
  padding: clamp(6px, 0.7vw, 13px);
  overflow: hidden;
  color: var(--ac-ink);
  background: #ddcbbb;
}

.atelier-craft button {
  color: inherit;
}

.atelier-craft__frame {
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(181, 138, 84, 0.64);
  border-radius: clamp(4px, 0.75vw, 14px);
  outline: none;
  background:
    radial-gradient(circle at 21% 44%, rgba(255, 255, 255, 0.88), transparent 30%),
    radial-gradient(circle at 82% 12%, rgba(239, 220, 216, 0.7), transparent 32%),
    linear-gradient(135deg, #fffaf2 0%, #f4e9dd 54%, #eee0d3 100%);
  box-shadow:
    0 32px 90px rgba(91, 67, 50, 0.12),
    inset 0 0 0 1px rgba(255, 255, 255, 0.52);
}

.atelier-craft__frame::before,
.atelier-craft__frame::after {
  position: absolute;
  z-index: 0;
  border: 1px solid rgba(181, 138, 84, 0.17);
  border-radius: 50%;
  content: '';
  pointer-events: none;
}

.atelier-craft__frame::before {
  top: -28%;
  right: -11%;
  width: 42vw;
  aspect-ratio: 1;
}

.atelier-craft__frame::after {
  right: 20%;
  bottom: -38%;
  width: 34vw;
  aspect-ratio: 1;
}

.atelier-craft__header {
  position: relative;
  z-index: 8;
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  min-height: 88px;
  padding: 0 clamp(24px, 4vw, 68px);
  border-bottom: 1px solid var(--ac-line);
  font-family: var(--ac-sans);
  opacity: 0;
  transform: translateY(-12px);
  transition: opacity 0.75s ease 0.12s, transform 0.85s ease 0.12s;
}

.atelier-craft.is-visible .atelier-craft__header {
  opacity: 1;
  transform: translateY(0);
}

.atelier-craft__brand {
  display: flex;
  flex-direction: column;
  margin: 0;
  text-transform: uppercase;
}

.atelier-craft__brand strong {
  font-family: var(--ac-serif);
  font-size: clamp(23px, 1.9vw, 32px);
  font-weight: 500;
  line-height: 1;
  letter-spacing: 0.08em;
}

.atelier-craft__brand span,
.atelier-craft__header-note,
.atelier-craft__header-links {
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

.atelier-craft__brand span {
  margin-top: 6px;
  color: rgba(69, 58, 51, 0.58);
}

.atelier-craft__header-note {
  margin: 0;
  padding: 9px 15px;
  border: 1px solid rgba(181, 138, 84, 0.36);
  border-radius: 999px;
  color: var(--ac-gold);
  background: rgba(255, 250, 242, 0.42);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

.atelier-craft__header-links {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 11px;
  color: rgba(69, 58, 51, 0.62);
}

.atelier-craft__header-links i {
  width: 3px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--ac-gold);
}

.atelier-craft__body {
  position: relative;
  z-index: 2;
  display: grid;
  grid-template-columns: minmax(0, 60%) minmax(350px, 40%);
  min-height: clamp(770px, 57vw, 920px);
}

.atelier-craft__wheel-column {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 0;
  overflow: hidden;
  border-right: 1px solid var(--ac-line);
}

.atelier-craft__wheel {
  position: relative;
  width: min(55vw, 860px);
  aspect-ratio: 1;
  flex: 0 0 auto;
  border: 1px solid rgba(181, 138, 84, 0.54);
  border-radius: 50%;
  box-shadow:
    0 36px 80px rgba(95, 69, 50, 0.15),
    0 0 0 12px rgba(255, 250, 242, 0.26);
  opacity: 0;
  transform: translateX(-8%) scale(0.94) rotate(-3deg);
  transition: opacity 1s ease 0.18s, transform 1.2s cubic-bezier(0.18, 0.76, 0.2, 1) 0.18s;
}

.atelier-craft.is-visible .atelier-craft__wheel {
  opacity: 1;
  transform: translateX(-8%) scale(1) rotate(0);
}

.atelier-craft__wheel > img {
  display: block;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  object-fit: cover;
}

.atelier-craft__dial {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 39%;
  aspect-ratio: 1;
  border: 1px solid rgba(181, 138, 84, 0.58);
  border-radius: 50%;
  background:
    radial-gradient(circle at 40% 32%, rgba(255, 255, 255, 0.95), rgba(248, 239, 226, 0.92) 62%, rgba(239, 220, 216, 0.9));
  box-shadow:
    0 24px 54px rgba(89, 65, 48, 0.15),
    inset 0 0 0 7px rgba(255, 255, 255, 0.38);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  transform: translate(-50%, -50%);
}

.atelier-craft__ticks {
  position: absolute;
  inset: 10px;
  border-radius: 50%;
  background: repeating-conic-gradient(from -0.5deg, rgba(181, 138, 84, 0.52) 0 1deg, transparent 1deg 15deg);
  mask: radial-gradient(transparent 0 87%, #000 88% 100%);
  opacity: 0.58;
}

.atelier-craft__marker {
  position: absolute;
  z-index: 5;
  top: 50%;
  left: 50%;
  width: 47%;
  height: 1px;
  padding: 0;
  border: 0;
  background: transparent;
  transform: rotate(var(--marker-angle));
  transform-origin: 0 50%;
  cursor: pointer;
}

.atelier-craft__marker span {
  position: absolute;
  right: 2px;
  display: block;
  min-width: 43px;
  padding: 4px 6px;
  border: 1px solid rgba(181, 138, 84, 0.22);
  border-radius: 999px;
  color: rgba(69, 58, 51, 0.58);
  background: rgba(255, 250, 242, 0.72);
  font-family: var(--ac-sans);
  font-size: clamp(6px, 0.48vw, 8px);
  font-weight: 600;
  letter-spacing: 0.08em;
  line-height: 1;
  text-align: center;
  text-transform: uppercase;
  transform: translate(50%, -50%) rotate(var(--marker-counter-angle));
  transition: color 0.25s ease, background 0.25s ease, border-color 0.25s ease;
}

.atelier-craft__marker:hover span,
.atelier-craft__marker.is-active span {
  border-color: var(--ac-gold);
  color: var(--ac-ivory);
  background: var(--ac-gold);
}

.atelier-craft__hand {
  position: absolute;
  z-index: 2;
  top: 50%;
  left: 50%;
  display: block;
  height: 1px;
  background: linear-gradient(90deg, var(--ac-gold), rgba(181, 138, 84, 0.2));
  transform-origin: 0 50%;
  transition: transform 0.85s cubic-bezier(0.2, 0.75, 0.2, 1);
}

.atelier-craft__hand::after {
  position: absolute;
  top: 50%;
  right: -3px;
  width: 6px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: var(--ac-gold);
  content: '';
  transform: translateY(-50%);
}

.atelier-craft__hand--long {
  width: 31%;
  transform: rotate(var(--dial-angle));
}

.atelier-craft__hand--short {
  width: 19%;
  background: linear-gradient(90deg, var(--ac-taupe), rgba(116, 101, 91, 0.12));
  transform: rotate(var(--dial-short-angle));
}

.atelier-craft__hand--short::after {
  background: var(--ac-taupe);
}

.atelier-craft__pin {
  position: absolute;
  z-index: 7;
  top: 50%;
  left: 50%;
  width: 14px;
  aspect-ratio: 1;
  border: 3px solid rgba(255, 250, 242, 0.82);
  border-radius: 50%;
  background: radial-gradient(circle at 36% 30%, #fff, #e1c9aa 58%, #a87945);
  box-shadow: 0 5px 12px rgba(84, 60, 44, 0.2);
  transform: translate(-50%, -50%);
}

.atelier-craft__readout {
  position: absolute;
  z-index: 4;
  top: 50%;
  left: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 49%;
  aspect-ratio: 1;
  padding: 8px;
  border: 1px solid rgba(255, 255, 255, 0.66);
  border-radius: 50%;
  background: rgba(255, 250, 242, 0.7);
  box-shadow: 0 14px 34px rgba(93, 68, 50, 0.11);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  text-align: center;
  transform: translate(-50%, -50%);
}

.atelier-craft__readout small {
  color: var(--ac-gold);
  font-family: var(--ac-sans);
  font-size: clamp(5px, 0.42vw, 7px);
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.atelier-craft__readout strong {
  max-width: 120px;
  margin-top: 5px;
  font-family: var(--ac-serif);
  font-size: clamp(13px, 1.35vw, 22px);
  font-weight: 400;
  line-height: 1.05;
}

.atelier-craft__wheel-caption {
  position: absolute;
  z-index: 6;
  right: clamp(24px, 3vw, 48px);
  bottom: clamp(23px, 3vw, 48px);
  display: flex;
  align-items: center;
  gap: 13px;
  padding: 12px 16px;
  border: 1px solid rgba(255, 255, 255, 0.66);
  border-radius: 999px;
  background: rgba(255, 250, 242, 0.66);
  box-shadow: 0 15px 36px rgba(91, 65, 48, 0.1);
  backdrop-filter: blur(15px);
  -webkit-backdrop-filter: blur(15px);
  opacity: 0;
  transform: translateY(14px);
  transition: opacity 0.7s ease 0.62s, transform 0.8s ease 0.62s;
}

.atelier-craft.is-visible .atelier-craft__wheel-caption {
  opacity: 1;
  transform: translateY(0);
}

.atelier-craft__wheel-caption > span {
  color: var(--ac-gold);
  font-family: var(--ac-serif);
  font-size: 26px;
}

.atelier-craft__wheel-caption > i {
  width: 1px;
  height: 30px;
  background: var(--ac-line);
}

.atelier-craft__wheel-caption p {
  margin: 0;
  color: rgba(69, 58, 51, 0.56);
  font-family: var(--ac-sans);
  font-size: 7px;
  line-height: 1.5;
  letter-spacing: 0.1em;
  text-transform: uppercase;
}

.atelier-craft__wheel-caption p strong {
  color: var(--ac-ink);
  font-weight: 600;
}

.atelier-craft__content {
  position: relative;
  display: flex;
  flex-direction: column;
  min-width: 0;
  padding: clamp(46px, 5vw, 82px) clamp(35px, 4.7vw, 76px) clamp(34px, 3.5vw, 58px);
}

.atelier-craft__copy {
  position: relative;
  z-index: 3;
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.85s ease 0.3s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.3s;
}

.atelier-craft.is-visible .atelier-craft__copy {
  opacity: 1;
  transform: translateY(0);
}

.atelier-craft__eyebrow {
  display: flex;
  align-items: center;
  gap: 11px;
  margin: 0;
  color: var(--ac-gold);
  font-family: var(--ac-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.atelier-craft__eyebrow i {
  width: 35px;
  height: 1px;
  background: currentColor;
}

.atelier-craft__copy h2 {
  margin: 18px 0 20px;
  font-family: var(--ac-serif);
  font-size: clamp(48px, 5vw, 82px);
  font-weight: 400;
  line-height: 0.88;
  letter-spacing: -0.055em;
}

.atelier-craft__copy h2 em {
  color: var(--ac-gold);
  font-weight: 400;
}

.atelier-craft__intro {
  max-width: 480px;
  margin: 0;
  color: rgba(69, 58, 51, 0.67);
  font-family: var(--ac-sans);
  font-size: clamp(11px, 0.86vw, 14px);
  line-height: 1.75;
}

.atelier-craft__active-step {
  position: relative;
  z-index: 3;
  display: grid;
  grid-template-columns: auto 1fr;
  gap: 20px;
  margin-top: clamp(28px, 3vw, 48px);
  padding: 19px 0 18px;
  border-top: 1px solid var(--ac-line);
  border-bottom: 1px solid var(--ac-line);
}

.atelier-craft__active-step > span {
  color: rgba(181, 138, 84, 0.48);
  font-family: var(--ac-serif);
  font-size: clamp(40px, 3.2vw, 54px);
  font-weight: 400;
  line-height: 0.9;
}

.atelier-craft__active-step small {
  color: var(--ac-gold);
  font-family: var(--ac-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.atelier-craft__active-step h3 {
  margin: 4px 0 6px;
  font-family: var(--ac-serif);
  font-size: clamp(22px, 2vw, 32px);
  font-weight: 400;
  line-height: 1;
}

.atelier-craft__active-step p {
  margin: 0;
  color: rgba(69, 58, 51, 0.61);
  font-family: var(--ac-sans);
  font-size: 10px;
  line-height: 1.58;
}

.atelier-craft__controls {
  position: relative;
  z-index: 3;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 20px;
  margin-top: 19px;
}

.atelier-craft__controls > div {
  display: flex;
  gap: 8px;
}

.atelier-craft__controls > div button {
  display: grid;
  place-items: center;
  width: 42px;
  aspect-ratio: 1;
  border: 1px solid var(--ac-line);
  border-radius: 50%;
  background: rgba(255, 250, 242, 0.42);
  cursor: pointer;
  transition: color 0.25s ease, background 0.25s ease, transform 0.25s ease;
}

.atelier-craft__controls > div button:hover {
  color: var(--ac-gold);
  background: rgba(255, 250, 242, 0.82);
  transform: translateY(-2px);
}

.atelier-craft__back {
  transform: rotate(180deg);
}

.atelier-craft__discover {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 0 0 7px;
  border: 0;
  border-bottom: 1px solid rgba(181, 138, 84, 0.72);
  background: transparent;
  font-family: var(--ac-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  cursor: pointer;
  transition: color 0.25s ease, gap 0.25s ease;
}

.atelier-craft__discover:hover {
  gap: 15px;
  color: var(--ac-gold);
}

.atelier-craft__maker-card {
  position: relative;
  z-index: 3;
  min-height: 235px;
  margin: auto 0 0;
  overflow: hidden;
  border: 1px solid rgba(181, 138, 84, 0.38);
  border-radius: 76px 5px 5px 5px;
  background: var(--ac-cream);
  box-shadow: 0 22px 52px rgba(89, 65, 48, 0.13);
  opacity: 0;
  transform: translateY(24px);
  transition: opacity 0.8s ease 0.62s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.62s;
}

.atelier-craft.is-visible .atelier-craft__maker-card {
  opacity: 1;
  transform: translateY(0);
}

.atelier-craft__maker-card > img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: 50% 63%;
  transition: transform 0.85s cubic-bezier(0.2, 0.7, 0.2, 1);
}

.atelier-craft__maker-card:hover > img {
  transform: scale(1.04);
}

.atelier-craft__maker-veil {
  position: absolute;
  inset: 0;
  background: linear-gradient(90deg, rgba(60, 44, 34, 0.5), transparent 76%);
}

.atelier-craft__maker-card figcaption {
  position: absolute;
  top: 50%;
  left: clamp(17px, 2vw, 30px);
  display: flex;
  flex-direction: column;
  width: min(210px, 66%);
  padding: 17px 19px;
  border: 1px solid rgba(255, 255, 255, 0.45);
  border-radius: 4px 32px 4px 4px;
  color: var(--ac-ivory);
  background: rgba(86, 65, 52, 0.3);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  transform: translateY(-50%);
}

.atelier-craft__maker-card figcaption small {
  font-family: var(--ac-sans);
  font-size: 7px;
  font-weight: 600;
  letter-spacing: 0.15em;
  text-transform: uppercase;
}

.atelier-craft__maker-card figcaption strong {
  margin: 7px 0 5px;
  font-family: var(--ac-serif);
  font-size: clamp(23px, 2vw, 31px);
  font-weight: 400;
  line-height: 1;
}

.atelier-craft__maker-card figcaption span {
  color: rgba(255, 250, 242, 0.78);
  font-family: var(--ac-sans);
  font-size: 8px;
  line-height: 1.5;
}

.atelier-craft button:focus-visible,
.atelier-craft__frame:focus-visible {
  outline: 2px solid var(--ac-gold);
  outline-offset: 4px;
}

@media (max-width: 1100px) {
  .atelier-craft__body {
    grid-template-columns: minmax(0, 58%) minmax(330px, 42%);
  }

  .atelier-craft__wheel {
    width: min(58vw, 720px);
  }

  .atelier-craft__content {
    padding-right: 34px;
    padding-left: 34px;
  }

  .atelier-craft__maker-card {
    min-height: 220px;
  }
}

@media (max-width: 860px) {
  .atelier-craft__header {
    grid-template-columns: 1fr auto;
  }

  .atelier-craft__header-note {
    display: none;
  }

  .atelier-craft__body {
    display: block;
    min-height: 0;
  }

  .atelier-craft__wheel-column {
    min-height: min(102vw, 780px);
    border-right: 0;
    border-bottom: 1px solid var(--ac-line);
  }

  .atelier-craft__wheel {
    width: min(90vw, 700px);
    transform: scale(0.94);
  }

  .atelier-craft.is-visible .atelier-craft__wheel {
    transform: scale(1);
  }

  .atelier-craft__wheel-caption {
    right: 6%;
    bottom: 4%;
  }

  .atelier-craft__content {
    min-height: 870px;
    padding: 68px 8% 52px;
  }

  .atelier-craft__copy h2 {
    font-size: clamp(58px, 10vw, 88px);
  }

  .atelier-craft__maker-card {
    min-height: 300px;
    margin-top: 48px;
  }
}

@media (max-width: 560px) {
  .atelier-craft {
    padding: 5px;
  }

  .atelier-craft__frame {
    border-radius: 4px;
  }

  .atelier-craft__header {
    min-height: 78px;
    padding: 0 18px;
  }

  .atelier-craft__brand strong {
    font-size: 23px;
  }

  .atelier-craft__header-links {
    gap: 7px;
    font-size: 6px;
  }

  .atelier-craft__wheel-column {
    min-height: 108vw;
  }

  .atelier-craft__wheel {
    width: 94vw;
  }

  .atelier-craft__dial {
    width: 40%;
  }

  .atelier-craft__marker span {
    min-width: 18px;
    width: 18px;
    height: 18px;
    padding: 0;
    border-color: rgba(181, 138, 84, 0.42);
    font-size: 0;
  }

  .atelier-craft__marker span::after {
    position: absolute;
    top: 50%;
    left: 50%;
    width: 4px;
    aspect-ratio: 1;
    border-radius: 50%;
    background: var(--ac-gold);
    content: '';
    transform: translate(-50%, -50%);
  }

  .atelier-craft__marker.is-active span::after {
    background: var(--ac-ivory);
  }

  .atelier-craft__readout {
    width: 56%;
  }

  .atelier-craft__readout strong {
    font-size: 12px;
  }

  .atelier-craft__wheel-caption {
    right: 14px;
    bottom: 14px;
    padding: 8px 11px;
  }

  .atelier-craft__wheel-caption > span {
    font-size: 20px;
  }

  .atelier-craft__wheel-caption p {
    font-size: 6px;
  }

  .atelier-craft__content {
    min-height: 845px;
    padding: 52px 22px 30px;
  }

  .atelier-craft__copy h2 {
    margin-top: 16px;
    font-size: clamp(48px, 15vw, 65px);
  }

  .atelier-craft__intro {
    font-size: 11px;
  }

  .atelier-craft__active-step {
    gap: 14px;
  }

  .atelier-craft__active-step > span {
    font-size: 37px;
  }

  .atelier-craft__active-step h3 {
    font-size: 24px;
  }

  .atelier-craft__controls {
    align-items: flex-end;
  }

  .atelier-craft__discover {
    max-width: 130px;
    text-align: left;
  }

  .atelier-craft__maker-card {
    min-height: 270px;
    border-radius: 58px 4px 4px 4px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .atelier-craft *,
  .atelier-craft *::before,
  .atelier-craft *::after {
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
    transition-delay: 0ms !important;
  }
}
</style>
