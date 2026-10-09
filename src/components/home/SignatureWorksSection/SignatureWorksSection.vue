<script setup>
import { nextTick, onBeforeUnmount, onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

const emit = defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`

const works = [
  {
    id: 'selene-ring',
    title: 'Selene Ring',
    category: 'Rings',
    note: 'Freshwater pearl · Champagne gold',
    image: 'images/7cbaa963aeaedd5bd94a4621c3a8c629.jpg',
    alt: 'Hand wearing the Selene freshwater pearl ring',
    position: '50% 48%',
  },
  {
    id: 'celeste-drops',
    title: 'Celeste Drops',
    category: 'Earrings',
    note: 'Pearl drop · Sculptural gold',
    image: 'images/23ea4f026cef5656c9086d5ccac3a845.jpg',
    alt: 'Model wearing architectural gold and pearl drop earrings',
    position: '50% 38%',
  },
  {
    id: 'aurelia-ring',
    title: 'Aurelia Ring',
    category: 'Rings',
    note: 'Baroque pearl · Sculptural setting',
    image: 'images/57bb56fd24b9c48165287894957d3cb1.jpg',
    alt: 'Hands displaying a sculptural baroque pearl ring',
    position: '50% 44%',
  },
  {
    id: 'luna-hoops',
    title: 'Luna Hoops',
    category: 'Earrings',
    note: 'Freshwater pearls · Polished gold',
    image: 'images/dff26a930f2486f429e48bcd9f9cd34d.jpg',
    alt: 'Model wearing a champagne gold hoop with freshwater pearls',
    position: '50% 38%',
  },
  {
    id: 'maris-necklace',
    title: 'Maris Necklace',
    category: 'Necklaces',
    note: 'Graduated pearls · Fine gold chain',
    image: 'images/lunara-marquee-01-wrist.webp',
    alt: 'Model in sage silk wearing a graduated pearl necklace',
    position: '50% 42%',
  },
]

const activeIndex = ref(2)
const stage = ref(null)
const cardElements = ref([])
let scrollFrame

function relativeOffset(index) {
  let offset = index - activeIndex.value
  const halfway = Math.floor(works.length / 2)

  if (offset > halfway) offset -= works.length
  if (offset < -halfway) offset += works.length

  return offset
}

function cardStyle(index) {
  const offset = relativeOffset(index)
  const distance = Math.abs(offset)

  return {
    '--card-x': `${offset * 92}%`,
    '--card-y': `${distance * 24}px`,
    '--card-rotation': `${offset * 3.4}deg`,
    '--card-scale': 1 - distance * 0.085,
    '--card-layer': 10 - distance,
    '--card-opacity': 1 - distance * 0.12,
  }
}

function setCardRef(element, index) {
  if (element) cardElements.value[index] = element
}

function centerMobileCard(index) {
  if (!stage.value || window.innerWidth > 760) return
  const card = cardElements.value[index]
  if (!card) return

  stage.value.scrollTo({
    left: card.offsetLeft - (stage.value.clientWidth - card.clientWidth) / 2,
    behavior: window.matchMedia('(prefers-reduced-motion: reduce)').matches ? 'auto' : 'smooth',
  })
}

function select(index) {
  activeIndex.value = (index + works.length) % works.length
  nextTick(() => centerMobileCard(activeIndex.value))
}

function previous() {
  select(activeIndex.value - 1)
}

function next() {
  select(activeIndex.value + 1)
}

function openWork(work, index) {
  if (activeIndex.value !== index) {
    select(index)
    return
  }

  emit('explore', work.category)
}

function updateMobileActive() {
  window.cancelAnimationFrame(scrollFrame)
  scrollFrame = window.requestAnimationFrame(() => {
    if (!stage.value || window.innerWidth > 760) return

    const stageCenter = stage.value.scrollLeft + stage.value.clientWidth / 2
    let nearestIndex = 0
    let nearestDistance = Number.POSITIVE_INFINITY

    cardElements.value.forEach((card, index) => {
      if (!card) return
      const cardCenter = card.offsetLeft + card.clientWidth / 2
      const distance = Math.abs(stageCenter - cardCenter)
      if (distance < nearestDistance) {
        nearestDistance = distance
        nearestIndex = index
      }
    })

    activeIndex.value = nearestIndex
  })
}

onMounted(() => {
  nextTick(() => centerMobileCard(activeIndex.value))
})

onBeforeUnmount(() => {
  window.cancelAnimationFrame(scrollFrame)
})
</script>

<template>
  <section
    class="signature-works"
    aria-labelledby="signature-works-title"
    tabindex="0"
    @keydown.left.prevent="previous"
    @keydown.right.prevent="next"
  >
    <div class="signature-works__frame">
      <header class="signature-works__header">
        <p>
          Five signatures, united by natural lustre and a quietly sculptural point of view.
          Designed to feel personal now—and remain beautiful for years.
        </p>

        <div>
          <span>Collection study · 01—05</span>
          <h2 id="signature-works-title">Signature <em>works.</em></h2>
        </div>
      </header>

      <div
        ref="stage"
        class="signature-works__stage"
        aria-label="Featured jewellery carousel"
        @scroll.passive="updateMobileActive"
      >
        <button
          v-for="(work, index) in works"
          :key="work.id"
          :ref="(element) => setCardRef(element, index)"
          class="work-card"
          :class="{ 'is-active': activeIndex === index }"
          :style="cardStyle(index)"
          type="button"
          :aria-label="`${activeIndex === index ? 'Shop' : 'View'} ${work.title}`"
          @click="openWork(work, index)"
        >
          <img
            :src="asset(work.image)"
            :alt="work.alt"
            :style="{ objectPosition: work.position }"
            loading="lazy"
          />
          <span class="work-card__veil" aria-hidden="true"></span>

          <span class="work-card__category">{{ work.category }}</span>
          <span class="work-card__number">{{ String(index + 1).padStart(2, '0') }}</span>

          <span class="work-card__copy">
            <small>{{ work.note }}</small>
            <strong>{{ work.title }}</strong>
            <span class="work-card__action">
              {{ activeIndex === index ? 'Explore piece' : 'Bring forward' }}
              <Icon name="arrow-up-right" :size="16" />
            </span>
          </span>
        </button>
      </div>

      <footer class="signature-works__controls">
        <span class="signature-works__counter" aria-live="polite">
          {{ String(activeIndex + 1).padStart(2, '0') }}
          <i></i>
          {{ String(works.length).padStart(2, '0') }}
        </span>

        <div>
          <button type="button" aria-label="Previous featured piece" @click="previous">
            <Icon class="arrow-back" name="arrow-right" :size="19" />
          </button>
          <button type="button" aria-label="Next featured piece" @click="next">
            <Icon name="arrow-right" :size="19" />
          </button>
        </div>

        <span class="signature-works__hint">Use arrows or swipe</span>
      </footer>
    </div>
  </section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz,wght@6..96,400;6..96,500&family=Manrope:wght@400;500;600&display=swap');

.signature-works {
  --works-ink: #473d36;
  --works-ivory: #fffaf3;
  --works-cream: #f7f0e5;
  --works-blush: #f1dfdc;
  --works-sage: #b8c0ac;
  --works-gold: #b28a58;
  --works-taupe: #796b61;
  --works-line: #c6a473;
  --works-display: 'Bodoni Moda', Didot, 'Bodoni MT', serif;
  --works-sans: 'Manrope', 'Helvetica Neue', Arial, sans-serif;
  padding: clamp(5px, 0.65vw, 12px);
  color: var(--works-ink);
  outline: none;
  background: #dccbbb;
}

.signature-works__frame {
  position: relative;
  min-height: clamp(750px, 96svh, 1020px);
  overflow: hidden;
  border: 1px solid rgb(178 138 88 / 0.44);
  background:
    radial-gradient(circle at 8% 16%, rgb(241 223 220 / 0.86), transparent 25%),
    radial-gradient(circle at 91% 84%, rgb(184 192 172 / 0.52), transparent 28%),
    linear-gradient(145deg, #fffdf8 0%, var(--works-cream) 54%, #f3eadf 100%);
}

.signature-works__frame::before,
.signature-works__frame::after {
  content: '';
  position: absolute;
  pointer-events: none;
}

.signature-works__frame::before {
  inset: 13px;
  border: 1px solid rgb(178 138 88 / 0.2);
}

.signature-works__frame::after {
  top: 0;
  left: 50%;
  width: 1px;
  height: 20%;
  background: linear-gradient(180deg, var(--works-line), transparent);
  opacity: 0.5;
}

.signature-works__header {
  position: relative;
  z-index: 20;
  display: grid;
  grid-template-columns: minmax(190px, 0.48fr) minmax(0, 2fr);
  align-items: end;
  gap: clamp(28px, 5vw, 90px);
  padding: clamp(52px, 6vw, 92px) clamp(26px, 5vw, 82px) 0;
}

.signature-works__header > p {
  max-width: 25rem;
  margin: 0 0 clamp(10px, 1.4vw, 24px);
  font: 400 clamp(0.76rem, 1vw, 0.96rem) / 1.65 var(--works-sans);
  color: var(--works-taupe);
  opacity: 0.9;
}

.signature-works__header > div > span {
  display: block;
  margin-left: 0.55em;
  font: 600 0.6rem / 1 var(--works-sans);
  letter-spacing: 0.24em;
  text-transform: uppercase;
  color: var(--works-gold);
  opacity: 0.9;
}

.signature-works__header > div {
  position: relative;
  padding: clamp(22px, 2.8vw, 40px) clamp(22px, 3.4vw, 52px) clamp(25px, 3.1vw, 44px);
  overflow: hidden;
 
}

.signature-works__header > div::after {
  content: '';
  position: absolute;
  right: -7%;
  bottom: -65%;
  width: 31%;
  aspect-ratio: 1;
  border: 1px solid rgb(178 138 88 / 0.22);
  border-radius: 50%;
}

.signature-works__header h2 {
  margin: 10px 0 0;
  color: #493f38;
  font: 400 clamp(4.5rem, 7.4vw, 8.5rem) / 0.78 var(--works-display);
  letter-spacing: -0.055em;
  text-transform: none;
  white-space: nowrap;
}

.signature-works__header h2 em {
  color: var(--works-gold);
  font-family: var(--works-display);
  font-weight: 400;
  text-transform: none;
}

.signature-works__stage {
  position: relative;
  z-index: 5;
  height: clamp(410px, 50vw, 600px);
  margin-top: clamp(58px, 7vw, 105px);
}

.work-card {
  --card-x: 0%;
  --card-y: 0px;
  --card-rotation: 0deg;
  --card-scale: 1;
  --card-layer: 10;
  --card-opacity: 1;
  position: absolute;
  top: 0;
  left: 50%;
  z-index: var(--card-layer);
  width: clamp(190px, 21.5vw, 318px);
  height: clamp(360px, 42vw, 560px);
  padding: 0;
  overflow: hidden;
  color: var(--works-ink);
  text-align: left;
  border: 1px solid rgb(178 138 88 / 0.44);
  border-radius: 50% 50% clamp(24px, 2.3vw, 34px) clamp(24px, 2.3vw, 34px) / 18% 18% clamp(24px, 2.3vw, 34px) clamp(24px, 2.3vw, 34px);
  background: var(--works-ivory);
  box-shadow:
    0 26px 68px rgb(92 70 49 / 0.15),
    inset 0 0 0 1px rgb(255 255 255 / 0.38);
  opacity: var(--card-opacity);
  cursor: pointer;
  transform:
    translateX(calc(-50% + var(--card-x)))
    translateY(var(--card-y))
    rotate(var(--card-rotation))
    scale(var(--card-scale));
  transform-origin: 50% 90%;
  transition:
    transform 650ms cubic-bezier(0.2, 0.75, 0.2, 1),
    opacity 450ms ease,
    box-shadow 450ms ease,
    border-color 450ms ease;
}

.work-card:hover,
.work-card:focus-visible {
  border-color: rgb(178 138 88 / 0.9);
  box-shadow:
    0 34px 88px rgb(92 70 49 / 0.22),
    inset 0 0 0 1px rgb(255 255 255 / 0.55);
}

.work-card:focus-visible {
  outline: 2px solid var(--works-gold);
  outline-offset: 5px;
}

.work-card.is-active {
  border-color: rgb(178 138 88 / 0.94);
  box-shadow:
    0 36px 90px rgb(92 70 49 / 0.23),
    inset 0 0 0 1px rgb(255 255 255 / 0.6);
}

.work-card > img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  display: block;
  object-fit: cover;
  filter: saturate(0.82) contrast(0.99) brightness(1.03);
  transition: transform 900ms cubic-bezier(0.2, 0.7, 0.2, 1), filter 500ms ease;
}

.work-card:hover > img,
.work-card.is-active > img {
  filter: saturate(0.96) contrast(1.01) brightness(1.02);
  transform: scale(1.035);
}

.work-card__veil {
  position: absolute;
  inset: 0;
  background:
    linear-gradient(180deg, rgb(69 55 45 / 0.15), transparent 24%),
    linear-gradient(0deg, rgb(255 250 243 / 0.66), transparent 42%);
  pointer-events: none;
}

.work-card__category,
.work-card__number {
  position: absolute;
  top: clamp(16px, 2vw, 25px);
  z-index: 1;
  font: 600 0.55rem / 1 var(--works-sans);
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.work-card__category {
  left: clamp(16px, 2vw, 25px);
  padding: 9px 13px;
  color: #5d5047;
  border: 1px solid rgb(178 138 88 / 0.46);
  border-radius: 999px;
  background: rgb(255 250 243 / 0.82);
  box-shadow: 0 8px 24px rgb(76 58 42 / 0.08);
  backdrop-filter: blur(10px);
}

.work-card__number {
  right: clamp(16px, 2vw, 25px);
  color: #fffaf3;
  text-shadow: 0 2px 14px rgb(52 36 27 / 0.42);
  opacity: 0.92;
}

.work-card__copy {
  position: absolute;
  right: clamp(10px, 1vw, 14px);
  bottom: clamp(10px, 1vw, 14px);
  left: clamp(10px, 1vw, 14px);
  z-index: 1;
  display: block;
  padding: clamp(14px, 1.45vw, 20px);
  color: #473d36;
  border: 1px solid rgb(178 138 88 / 0.28);
  border-radius: clamp(15px, 1.4vw, 20px) clamp(15px, 1.4vw, 20px) clamp(20px, 2vw, 28px) clamp(20px, 2vw, 28px);
  background:
    linear-gradient(135deg, rgb(255 253 248 / 0.84), rgb(247 240 229 / 0.72));
  box-shadow: 0 15px 42px rgb(71 54 38 / 0.13);
  backdrop-filter: blur(18px) saturate(1.08);
  -webkit-backdrop-filter: blur(18px) saturate(1.08);
}

.work-card__copy small {
  display: block;
  margin-bottom: 9px;
  font: 600 clamp(0.5rem, 0.58vw, 0.59rem) / 1.35 var(--works-sans);
  letter-spacing: 0.13em;
  text-transform: uppercase;
  color: #8a725d;
  opacity: 0.92;
}

.work-card__copy strong {
  display: block;
  font: 400 clamp(1.52rem, 2.05vw, 2.4rem) / 1 var(--works-display);
  letter-spacing: -0.035em;
}

.work-card__action {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  max-height: 38px;
  margin-top: 12px;
  overflow: hidden;
  font: 600 0.54rem / 1 var(--works-sans);
  letter-spacing: 0.14em;
  text-transform: uppercase;
  color: #9b7448;
  opacity: 0.78;
  transition: max-height 350ms ease, margin 350ms ease, opacity 350ms ease;
}

.work-card.is-active .work-card__action,
.work-card:focus-visible .work-card__action {
  max-height: 38px;
  margin-top: 12px;
  opacity: 1;
}

.work-card__action :deep(svg) {
  width: 32px;
  height: 32px;
  padding: 8px;
  flex: 0 0 auto;
  border: 1px solid rgb(178 138 88 / 0.46);
  border-radius: 50%;
  background: rgb(255 253 248 / 0.6);
}

.signature-works__controls {
  position: absolute;
  right: clamp(30px, 5vw, 82px);
  bottom: clamp(30px, 4vw, 58px);
  left: clamp(30px, 5vw, 82px);
  z-index: 20;
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 28px;
}

.signature-works__counter {
  display: flex;
  align-items: center;
  gap: 11px;
  font: 600 0.62rem / 1 var(--works-sans);
  letter-spacing: 0.13em;
  color: var(--works-taupe);
}

.signature-works__counter i {
  width: 38px;
  height: 1px;
  background: currentColor;
  opacity: 0.36;
}

.signature-works__controls > div {
  display: flex;
  gap: 9px;
}

.signature-works__controls button {
  display: grid;
  width: 56px;
  aspect-ratio: 1;
  padding: 0;
  place-items: center;
  color: #765b40;
  border: 1px solid rgb(178 138 88 / 0.5);
  border-radius: 50%;
  background: rgb(255 250 243 / 0.78);
  box-shadow: 0 8px 24px rgb(87 66 47 / 0.1);
  cursor: pointer;
  transition: color 250ms ease, background-color 250ms ease, border-color 250ms ease;
}

.signature-works__controls button:hover,
.signature-works__controls button:focus-visible {
  color: #fffaf3;
  border-color: var(--works-gold);
  outline: none;
  background: var(--works-gold);
}

.arrow-back {
  transform: rotate(180deg);
}

.signature-works__hint {
  justify-self: end;
  font: 600 0.57rem / 1 var(--works-sans);
  letter-spacing: 0.15em;
  text-transform: uppercase;
  color: var(--works-taupe);
  opacity: 0.68;
}

@media (max-width: 1050px) {
  .signature-works__frame {
    min-height: 820px;
  }

  .signature-works__header h2 {
    font-size: clamp(3.8rem, 7vw, 5.4rem);
  }

  .signature-works__stage {
    margin-top: 85px;
  }
}

@media (max-width: 760px) {
  .signature-works {
    padding: 5px;
  }

  .signature-works__frame {
    min-height: auto;
    padding-bottom: 34px;
  }

  .signature-works__header {
    grid-template-columns: 1fr;
    gap: 28px;
    padding: 58px 24px 0;
  }

  .signature-works__header > p {
    max-width: 21rem;
    order: 2;
  }

  .signature-works__header h2 {
    font-size: clamp(3.65rem, 18vw, 6.2rem);
    line-height: 0.78;
    white-space: normal;
  }

  .signature-works__stage {
    display: flex;
    height: auto;
    gap: 16px;
    margin-top: 58px;
    padding: 0 12vw 30px;
    overflow-x: auto;
    overscroll-behavior-inline: contain;
    scroll-padding-inline: 12vw;
    scroll-snap-type: x mandatory;
    scrollbar-width: none;
  }

  .signature-works__stage::-webkit-scrollbar {
    display: none;
  }

  .work-card {
    position: relative;
    top: auto;
    left: auto;
    flex: 0 0 min(76vw, 330px);
    width: min(76vw, 330px);
    height: min(126vw, 560px);
    min-height: 480px;
    opacity: 1;
    scroll-snap-align: center;
    transform: none;
  }

  .signature-works__controls {
    position: relative;
    right: auto;
    bottom: auto;
    left: auto;
    grid-template-columns: 1fr auto;
    margin: 10px 24px 0;
  }

  .signature-works__controls > div {
    grid-column: 2;
    grid-row: 1;
  }

  .signature-works__controls button {
    width: 50px;
  }

  .signature-works__hint {
    display: none;
  }
}

@media (max-width: 420px) {
  .signature-works__header {
    padding-inline: 20px;
  }

  .signature-works__header h2 {
    font-size: clamp(3.25rem, 17vw, 4.4rem);
  }

  .work-card {
    flex-basis: 80vw;
    width: 80vw;
    height: 132vw;
  }

  .signature-works__controls {
    margin-inline: 20px;
  }
}

@media (prefers-reduced-motion: reduce) {
  .work-card,
  .work-card > img,
  .work-card__action,
  .signature-works__controls button {
    scroll-behavior: auto;
    transition: none;
  }
}
</style>
