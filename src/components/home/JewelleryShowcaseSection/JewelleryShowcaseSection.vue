<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`

const slides = [
  
 {
  key: 'earrings',
  category: 'Earrings',
  eyebrow: 'The light edit',
  title: 'Earrings',
  statement: 'Light, held close.',

  copy:
    'Pearls framed in warm gold, designed to catch the light with every turn and feel effortless from morning to evening.',

  primaryImage: 'images/45570f4b3a6ee62d5468c5f5a068d211.jpg',
  primaryFallback: 'images/45570f4b3a6ee62d5468c5f5a068d211.jpg',
  primaryAlt: 'Model wearing elegant gold and pearl hoop earrings',

  detailImage: 'images/lunara-reimagined-earrings.webp',
  detailFallback: 'images/luna-studs.webp',
  detailAlt: 'Gold hoop earrings detailed with freshwater pearls',

  tone: 'champagne',

  position: '58% center',
},
  {
    key: 'necklaces',
    category: 'Necklaces',
    eyebrow: 'The signature strand',
    title: 'Necklaces',
    statement: 'An icon, softened.',
    copy:
      'Classic freshwater pearls rebalanced with modern proportions—quietly polished pieces made for open collars and evening light.',
    primaryImage: 'images/e8ad355af98099448d7719afd02e9341.jpg',
    primaryFallback: 'images/organic-strand.webp',
    primaryAlt: 'Close portrait styled with a modern pearl necklace',
    detailImage: 'images/gift-presentation.webp',
    detailFallback: 'images/classic-strand.webp',
    detailAlt: 'Organic fre.shwater pearl necklace in soft natural light',
    tone: 'sage',
    position: 'center 36%',
  },
  {
    key: 'rings',
    category: 'Rings',
    eyebrow: 'The modern heirloom',
    title: 'Rings',
    statement: 'One perfect detail.',
    copy:
      'Sculptural settings, luminous pearls and a restrained gold finish—small statements created to become entirely your own.',
    primaryImage: 'images/fe607360adb3f91f816e4051a7ee7bca.jpg',
    primaryFallback: 'images/0e359ddfeda81c9c5c2e1b3332504cdb.jpg',
    primaryAlt: 'Pearl solitaire ring photographed in warm editorial light',
    detailImage: 'images/lunara-reimagined-rings.webp',
    detailFallback: 'images/0e359ddfeda81c9c5c2e1b3332504cdb.jpg',
    detailAlt: 'Lunara jewellery presented in an ivory keepsake box',
    tone: 'blush',
    position: 'center',
  },
]

const activeIndex = ref(0)
const slideElements = ref([])
let scrollFrame

function setSlideRef(element, index) {
  if (element) {
    slideElements.value[index] = element
  }
}

function updateActiveSlide() {
  let nextIndex = 0
  const activationLine = window.innerHeight * 0.22

  slideElements.value.forEach((element, index) => {
    if (element?.getBoundingClientRect().top <= activationLine) {
      nextIndex = index
    }
  })

  activeIndex.value = nextIndex
}

function handleScroll() {
  window.cancelAnimationFrame(scrollFrame)
  scrollFrame = window.requestAnimationFrame(updateActiveSlide)
}

function goToSlide(index) {
  slideElements.value[index]?.scrollIntoView({
    behavior: window.matchMedia('(prefers-reduced-motion: reduce)').matches
      ? 'auto'
      : 'smooth',
    block: 'start',
  })
}

function useImageFallback(event, fallbackPath) {
  const image = event.currentTarget

  if (image.dataset.fallbackApplied === 'true') {
    image.classList.add('is-missing')
    image.removeAttribute('src')
    return
  }

  image.dataset.fallbackApplied = 'true'
  image.src = asset(fallbackPath)
}

onMounted(() => {
  updateActiveSlide()

  window.addEventListener('scroll', handleScroll, {
    passive: true,
  })

  window.addEventListener('resize', handleScroll)
})

onBeforeUnmount(() => {
  window.removeEventListener('scroll', handleScroll)
  window.removeEventListener('resize', handleScroll)
  window.cancelAnimationFrame(scrollFrame)
})
</script>

<template>
  <section
    class="scroll-showcase"
    aria-labelledby="scroll-showcase-title"
  >
    <h2
      id="scroll-showcase-title"
      class="sr-only"
    >
      Explore the Lunara jewellery edits
    </h2>

    <article
      v-for="(slide, index) in slides"
      :key="slide.key"
      :ref="(element) => setSlideRef(element, index)"
      class="showcase-slide"
      :class="[
        `showcase-slide--${slide.tone}`,
        { 'is-active': activeIndex === index },
      ]"
      :style="{ '--slide-layer': index + 1 }"
    >
      <div class="showcase-slide__visual">
        <img
          :src="asset(slide.primaryImage)"
          :alt="slide.primaryAlt"
          :style="{ objectPosition: slide.position }"
          loading="eager"
          :fetchpriority="index === 0 ? 'high' : 'auto'"
          @error="useImageFallback($event, slide.primaryFallback)"
        />

        <span
          class="showcase-slide__shade"
          aria-hidden="true"
        ></span>

        <div
          class="showcase-slide__signature"
          aria-hidden="true"
        >
          <span>LUNARA</span>
          <small>Fine Pearl Jewellery</small>
        </div>

        <div class="showcase-slide__title">
          <span>
            {{ String(index + 1).padStart(2, '0') }}
          </span>

          <h3>{{ slide.title }}</h3>

          <p>{{ slide.statement }}</p>
        </div>
      </div>

      <div class="showcase-slide__story">
        <div class="showcase-slide__topline">
          <span>Lunara Collection Study</span>

          <span>
            {{ String(index + 1).padStart(2, '0') }}
            /
            {{ String(slides.length).padStart(2, '0') }}
          </span>
        </div>

        <div class="showcase-slide__detail">
          <span
            class="detail-glow detail-glow--one"
            aria-hidden="true"
          ></span>

          <span
            class="detail-glow detail-glow--two"
            aria-hidden="true"
          ></span>

          <div class="detail-frame">
            <img
              :src="asset(slide.detailImage)"
              :alt="slide.detailAlt"
              loading="eager"
              @error="useImageFallback($event, slide.detailFallback)"
            />

            <span
              class="detail-frame__wash"
              aria-hidden="true"
            ></span>
          </div>

          <div class="detail-caption">
            <span>Natural pearl</span>

            <i></i>

            <span>Finished by hand</span>
          </div>
        </div>

        <div class="showcase-slide__copy">
          <p class="showcase-slide__eyebrow">
            {{ slide.eyebrow }}
          </p>

          <h3>{{ slide.statement }}</h3>

          <p>
            {{ slide.copy }}
          </p>

          <button
            class="showcase-slide__cta"
            type="button"
            @click="$emit('explore', slide.category)"
          >
            <span class="showcase-slide__cta-text">
              Shop {{ slide.category.toLowerCase() }}
            </span>

            <span class="showcase-slide__cta-icon">
              <Icon
                name="arrow-up-right"
                :size="17"
              />
            </span>
          </button>
        </div>

        <div
          class="showcase-slide__navigation"
          aria-label="Choose collection panel"
        >
          <button
            v-for="(_, dotIndex) in slides"
            :key="dotIndex"
            type="button"
            :class="{ active: activeIndex === dotIndex }"
            :aria-label="`Go to ${slides[dotIndex].title}`"
            :aria-current="
              activeIndex === dotIndex
                ? 'step'
                : undefined
            "
            @click="goToSlide(dotIndex)"
          >
            <span></span>

            {{ String(dotIndex + 1).padStart(2, '0') }}
          </button>
        </div>
      </div>
    </article>
  </section>
</template>

<style scoped>
.scroll-showcase {
  --pearl: #fffdf9;
  --ivory: #fbf6ef;
  --cream: #f7eddf;
  --blush: #f5e8e5;
  --sage: #e7eae3;

  --gold: #b79561;
  --gold-soft: #d5bf9c;
  --gold-dark: #927147;

  --ink: #40372f;
  --taupe: #786c62;

  position: relative;
  isolation: isolate;

  background: var(--pearl);
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

/* ======================================================
   SLIDE
====================================================== */

.showcase-slide {
  --panel: #f2e5d4;
  --panel-soft: #fdf8f1;
  --accent: #a37d4d;
  --light: #fffdf8;

  position: sticky;
  top: 0;
  z-index: var(--slide-layer);

  display: grid;

  grid-template-columns:
    minmax(0, 1.08fr)
    minmax(390px, 0.92fr);

  width: 100%;

  /* IMPORTANT */
  height: 100svh;
  min-height: 100svh;

  overflow: hidden;

  color: var(--ink);
  background: var(--pearl);
}

.showcase-slide--sage {
  --panel: #e3e7df;
  --panel-soft: #fafbf7;
  --accent: #77816f;
  --light: #fffdf9;
}

.showcase-slide--blush {
  --panel: #f0e0dc;
  --panel-soft: #fff8f5;
  --accent: #9c7168;
  --light: #fffaf7;
}

.showcase-slide__visual,
.showcase-slide__story {
  min-width: 0;
  min-height: 0;
  height: 100svh;
}

/* ======================================================
   LEFT IMAGE
====================================================== */

.showcase-slide__visual {
  position: relative;

  overflow: hidden;

  background: var(--ivory);
}

.showcase-slide__visual > img {
  width: 100%;
  height: 100%;

  min-height: 100svh;

  display: block;

  object-fit: cover;

  filter:
    saturate(0.96)
    contrast(0.99)
    brightness(1.03);

  transform: scale(1.01);

  transition:
    transform 1.25s
      cubic-bezier(0.22, 1, 0.36, 1),
    filter 700ms ease;
}

.showcase-slide.is-active
.showcase-slide__visual > img {
  transform: scale(1);

  filter:
    saturate(1)
    contrast(1)
    brightness(1.03);
}

.showcase-slide__visual > img.is-missing {
  visibility: hidden;
}

.showcase-slide__shade {
  position: absolute;

  inset: 0;

  pointer-events: none;

  background:
    linear-gradient(
      180deg,
      rgba(255, 251, 245, 0.08),
      transparent 34%
    ),
    linear-gradient(
      0deg,
      rgba(54, 41, 33, 0.37),
      rgba(54, 41, 33, 0.06) 48%,
      transparent 68%
    );
}

/* ======================================================
   LEFT BRAND
====================================================== */

.showcase-slide__signature {
  position: absolute;

  top:
    clamp(
      28px,
      4vw,
      56px
    );

  left:
    clamp(
      28px,
      5vw,
      72px
    );

  display: flex;

  flex-direction: column;

  gap: 6px;

  color: #fffdf8;
}

.showcase-slide__signature > span {
  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.69rem;

  font-weight: 600;

  letter-spacing: 0.31em;
}

.showcase-slide__signature small {
  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.47rem;

  font-weight: 500;

  letter-spacing: 0.28em;

  text-transform: uppercase;

  opacity: 0.78;
}

/* ======================================================
   LEFT TEXT
====================================================== */

.showcase-slide__title {
  position: absolute;

  right:
    clamp(
      28px,
      5vw,
      72px
    );

  bottom:
    clamp(
      38px,
      6vw,
      82px
    );

  left:
    clamp(
      28px,
      5vw,
      72px
    );

  color: #fffdf9;
}

.showcase-slide__title > span {
  display: block;

  margin-bottom: 10px;

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.61rem;

  font-weight: 600;

  letter-spacing: 0.22em;
}

.showcase-slide__title h3 {
  margin: 0;

  font-family:
    var(
      --font-display,
      'Cormorant Garamond',
      Georgia,
      serif
    );

  font-size:
    clamp(
      4rem,
      8vw,
      8.6rem
    );

  font-weight: 400;

  line-height: 0.84;

  letter-spacing: -0.05em;
}

.showcase-slide__title p {
  margin:
    clamp(
      18px,
      2.5vw,
      28px
    )
    0
    0;

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size:
    clamp(
      0.7rem,
      0.84vw,
      0.83rem
    );

  font-weight: 500;

  line-height: 1.45;

  letter-spacing: 0.16em;

  text-transform: uppercase;
}

/* ======================================================
   RIGHT PANEL
====================================================== */

.showcase-slide__story {
  position: relative;

  display: grid;

  grid-template-rows:
    auto
    minmax(0, 1fr)
    auto
    auto;

  gap: clamp(10px, 1.25vw, 18px);

  height: 100svh;
  min-height: 0;

  padding:
    clamp(26px, 3vw, 42px)
    clamp(32px, 4.2vw, 64px)
    clamp(24px, 3vw, 40px);

  overflow: hidden;

  background:
    radial-gradient(
      circle at 22% 17%,
      rgba(255, 255, 255, 0.82),
      transparent 30%
    ),
    radial-gradient(
      circle at 90% 82%,
      rgba(183, 149, 97, 0.045),
      transparent 32%
    ),
    linear-gradient(
      145deg,
      var(--panel-soft) 0%,
      var(--panel) 100%
    );
}

.showcase-slide__story::before {
  content: '';

  position: absolute;

  inset: 0;

  pointer-events: none;

  opacity: 0.28;

  background:
    linear-gradient(
      120deg,
      transparent 0%,
      rgba(255, 255, 255, 0.2) 40%,
      transparent 56%
    );
}

.showcase-slide__topline,
.showcase-slide__copy,
.showcase-slide__navigation {
  position: relative;

  z-index: 2;
}
.showcase-slide__detail {
  position: relative;

  display: flex;
  align-items: center;
  justify-content: center;

  min-height: 0;

  padding: 6px 0;
}

/* ======================================================
   TOP LINE
====================================================== */

.showcase-slide__topline {
  display: flex;

  align-items: center;

  justify-content: space-between;

  gap: 1rem;

  padding-bottom: 14px;

  border-bottom:
    1px solid
    rgba(146, 113, 71, 0.18);

  color: var(--taupe);

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.56rem;

  font-weight: 600;

  letter-spacing: 0.2em;

  text-transform: uppercase;
}

/* ======================================================
   PRODUCT AREA
====================================================== */

.showcase-slide__detail {
  display: flex;

  align-items: center;

  justify-content: center;

  min-height: 360px;

  padding:
    clamp(
      14px,
      2vw,
      26px
    )
    0;
}

.detail-frame {
  position: relative;
  z-index: 2;

  width: min(58%, 310px);

  height: clamp(
    270px,
    36vh,
    360px
  );

  min-height: 0;

  overflow: hidden;

  border:
    1px solid
    rgba(183, 149, 97, 0.28);

  border-radius:
    48% 48% 16px 16px /
    30% 30% 16px 16px;

  background: var(--pearl);

  box-shadow:
    0 24px 55px rgba(91, 67, 44, 0.09),
    0 5px 18px rgba(91, 67, 44, 0.03);

  transform: translateY(8px);

  opacity: 0.7;

  transition:
    transform 900ms cubic-bezier(0.22, 1, 0.36, 1),
    opacity 700ms ease;
}

.showcase-slide.is-active
.detail-frame {
  transform:
    translateY(0)
    scale(1);

  opacity: 1;
}

.detail-frame img {
  width: 100%;
  height: 100%;

  display: block;

  object-fit: cover;

  object-position: center;

  filter:
    saturate(0.96)
    brightness(1.02);

  transition:
    transform
    1s
    cubic-bezier(0.22, 1, 0.36, 1);
}

.detail-frame:hover img {
  transform: scale(1.035);
}

.detail-frame img.is-missing {
  visibility: hidden;
}

.detail-frame__wash {
  position: absolute;

  inset: 0;

  pointer-events: none;

  background:
    linear-gradient(
      145deg,
      rgba(255, 255, 255, 0.2),
      transparent 42%
    );
}

/* ======================================================
   NATURAL BACKGROUND GLOW
====================================================== */

.detail-glow {
  position: absolute;

  pointer-events: none;

  border-radius: 50%;

  filter: blur(12px);
}

.detail-glow--one {
  width:
    clamp(
      120px,
      15vw,
      210px
    );

  aspect-ratio: 1;

  top: 3%;
  left: 8%;

  background:
    rgba(255, 255, 255, 0.26);
}

.detail-glow--two {
  width:
    clamp(
      100px,
      12vw,
      170px
    );

  aspect-ratio: 1;

  right: 8%;
  bottom: 4%;

  background:
    rgba(183, 149, 97, 0.045);
}

/* ======================================================
   PRODUCT CAPTION
====================================================== */

.detail-caption {
  position: absolute;

  z-index: 5;

  bottom: 1%;

  left: 50%;

  display: inline-flex;

  align-items: center;

  gap: 9px;

  width: max-content;

  padding:
    8px
    13px;

  border:
    1px solid
    rgba(183, 149, 97, 0.2);

  border-radius: 999px;

  background:
    rgba(255, 253, 249, 0.84);

  box-shadow:
    0 8px 22px
    rgba(84, 61, 40, 0.045);

  color: var(--taupe);

  transform:
    translateX(-50%);

  backdrop-filter: blur(10px);

  -webkit-backdrop-filter: blur(10px);

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.47rem;

  font-weight: 600;

  letter-spacing: 0.12em;

  text-transform: uppercase;
}

.detail-caption i {
  width: 5px;
  height: 5px;

  display: block;

  border-radius: 50%;

  background: var(--accent);
}

/* ======================================================
   COPY
====================================================== */

.showcase-slide__copy {
  position: relative;
  z-index: 5;

  max-width: 33rem;

  margin: 0;
}

.showcase-slide__eyebrow {
  margin: 0 0 7px;

  color: var(--accent);

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.57rem;
  font-weight: 600;

  letter-spacing: 0.19em;

  text-transform: uppercase;
}

.showcase-slide__copy h3 {
  max-width: 11ch;

  margin: 0;

  color: #40372f;

  font-family:
    var(
      --font-display,
      'Cormorant Garamond',
      Georgia,
      serif
    );

  font-size:
    clamp(
      2rem,
      2.8vw,
      3.35rem
    );

  font-weight: 400;

  line-height: 1;

  letter-spacing: -0.035em;
}

.showcase-slide__copy
> p:not(.showcase-slide__eyebrow) {
  max-width: 29rem;

  margin: 10px 0 16px;

  color: #786c62;

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size:
    clamp(
      0.76rem,
      0.86vw,
      0.9rem
    );

  line-height: 1.62;
}

/* ======================================================
   CTA
====================================================== */

.showcase-slide__cta {
  display: inline-flex;

  align-items: center;

  gap: 13px;

  padding: 0;

  border: 0;

  background: transparent;

  color: var(--ink);

  cursor: pointer;
}

.showcase-slide__cta-text {
  padding-bottom: 4px;

  border-bottom:
    1px solid
    rgba(64, 55, 47, 0.28);

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.64rem;

  font-weight: 600;

  letter-spacing: 0.14em;

  text-transform: uppercase;
}

.showcase-slide__cta-icon {
  width: 40px;

  aspect-ratio: 1;

  display: grid;

  place-items: center;

  border:
    1px solid
    rgba(183, 149, 97, 0.45);

  border-radius: 50%;

  color: var(--gold-dark);

  background:
    rgba(255, 253, 249, 0.5);

  transition:
    color 250ms ease,
    background 250ms ease,
    border-color 250ms ease,
    transform 250ms ease;
}

.showcase-slide__cta:hover
.showcase-slide__cta-icon,
.showcase-slide__cta:focus-visible
.showcase-slide__cta-icon {
  color: #fff;

  background: var(--accent);

  border-color: var(--accent);

  transform:
    translate(
      2px,
      -2px
    );
}

/* ======================================================
   NAVIGATION
====================================================== */

.showcase-slide__navigation {
  display: flex;

  align-items: center;

  justify-self: end;

  gap: 17px;
}

.showcase-slide__navigation button {
  display: inline-flex;

  align-items: center;

  gap: 7px;

  padding: 0;

  border: 0;

  background: transparent;

  color: var(--taupe);

  cursor: pointer;

  opacity: 0.42;

  font-family:
    var(
      --font-sans,
      'Helvetica Neue',
      Arial,
      sans-serif
    );

  font-size: 0.55rem;

  font-weight: 600;

  letter-spacing: 0.09em;

  transition:
    opacity
    250ms ease;
}

.showcase-slide__navigation
button span {
  width: 0;
  height: 1px;

  background:
    var(--gold-dark);

  transition:
    width
    300ms ease;
}

.showcase-slide__navigation
button.active {
  color: var(--ink);

  opacity: 1;
}

.showcase-slide__navigation
button.active span {
  width: 24px;
}

.showcase-slide__cta:focus-visible,
.showcase-slide__navigation
button:focus-visible {
  outline:
    2px solid
    var(--gold);

  outline-offset: 5px;
}

/* ======================================================
   TABLET
====================================================== */

@media (max-width: 900px) {
  .showcase-slide {
    position: relative;

    top: auto;

    grid-template-columns:
      1fr;

    min-height: auto;
  }

  .showcase-slide__visual {
    min-height: 66svh;
  }

  .showcase-slide__visual > img {
    min-height: 66svh;
  }

  .showcase-slide__story {
    grid-template-rows:
      auto
      auto
      auto
      auto;

    min-height: auto;

    padding:
      30px
      clamp(
        24px,
        6vw,
        54px
      )
      38px;
  }

  .showcase-slide__detail {
    min-height: 420px;

    padding:
      30px
      0;
  }

  .detail-frame {
    width:
      min(
        58vw,
        330px
      );

    height: 390px;

    min-height: 0;
  }
.showcase-slide__title {
  position: absolute;

  right: clamp(28px, 5vw, 72px);
  bottom: clamp(30px, 4vw, 56px);
  left: clamp(28px, 5vw, 72px);

  z-index: 5;

  color: #fffdf9;
}
}

/* ======================================================
   MOBILE
====================================================== */

@media (max-width: 560px) {
  .showcase-slide__visual {
    min-height: 70svh;
  }

  .showcase-slide__visual > img {
    min-height: 70svh;
  }

  .showcase-slide__signature {
    top: 23px;
    left: 21px;
  }

  .showcase-slide__title {
    right: 21px;
    bottom: 28px;
    left: 21px;
  }

  .showcase-slide__title h3 {
    font-size:
      clamp(
        3.6rem,
        18vw,
        5.8rem
      );
  }

  .showcase-slide__story {
    gap: 21px;

    padding:
      24px
      20px
      30px;
  }

  .showcase-slide__topline
  span:first-child {
    display: none;
  }

  .showcase-slide__topline {
    justify-content:
      flex-end;
  }

  .showcase-slide__detail {
    min-height: 345px;

    padding:
      16px
      0;
  }

  .detail-frame {
    width:
      min(
        70vw,
        275px
      );

    height: 320px;

    min-height: 0;

    border-radius:
      47% 47% 14px 14px /
      28% 28% 14px 14px;
  }

  .detail-caption {
    bottom: 0;

    padding:
      7px
      10px;

    font-size: 0.41rem;
  }

  .showcase-slide__copy h3 {
    font-size:
      clamp(
        2.3rem,
        11vw,
        3.25rem
      );
  }

  .showcase-slide__navigation {
    justify-self: start;
  }
}

/* ======================================================
   SMALL MOBILE
====================================================== */

@media (max-width: 380px) {
  .detail-frame {
    width:
      min(
        69vw,
        250px
      );

    height: 295px;
  }

  .detail-caption {
    gap: 6px;

    font-size: 0.39rem;
  }
}

/* ======================================================
   REDUCED MOTION
====================================================== */

@media (prefers-reduced-motion: reduce) {
  .showcase-slide__visual > img,
  .detail-frame,
  .detail-frame img,
  .showcase-slide__cta-icon,
  .showcase-slide__navigation
  button span {
    transition: none;
  }
}
</style>