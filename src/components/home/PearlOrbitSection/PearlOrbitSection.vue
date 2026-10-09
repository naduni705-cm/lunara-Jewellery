<script setup>
import { onBeforeUnmount, onMounted, ref } from 'vue'
import Icon from '../../shared/Icon/Icon.vue'

defineEmits(['explore'])

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const section = ref(null)
const isVisible = ref(false)
let revealObserver

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
    { threshold: 0.2 },
  )

  revealObserver.observe(section.value)
})

onBeforeUnmount(() => revealObserver?.disconnect())
</script>

<template>
  <section
    ref="section"
    class="pearl-orbit"
    :class="{ 'is-visible': isVisible }"
    aria-labelledby="pearl-orbit-title"
  >
    <div class="pearl-orbit__stage">
      <img
        class="pearl-orbit__background"
        :src="asset('images/lunara-orbit-stage.webp')"
        alt=""
        loading="lazy"
        decoding="async"
      />
      <span class="pearl-orbit__veil" aria-hidden="true"></span>

      <header class="pearl-orbit__header">
        <p class="pearl-orbit__brand">
          <strong>Lunara</strong>
          <span>Pearl atelier</span>
        </p>

        <p class="pearl-orbit__study">Form study · 07</p>

        <p class="pearl-orbit__meta">
          <span>Fine jewellery</span>
          <span>Small editions</span>
        </p>
      </header>

      <h2 id="pearl-orbit-title" class="pearl-orbit__title">
        <span>Eternal</span>
        <em>Lustre.</em>
      </h2>

      <figure class="pearl-orbit__jewel">
        <img
          :src="asset('images/lunara-orbit-rings.png')"
          alt="A platinum pavé pearl ring and a champagne-gold twin-pearl ring"
          loading="lazy"
          decoding="async"
        />
        <figcaption>Akoya pearl · Pavé diamond · Mixed precious metals</figcaption>
      </figure>

      <article class="pearl-orbit__story">
        <div class="pearl-orbit__eyebrow">
          <i aria-hidden="true"></i>
          <span>The Orbit pairing</span>
        </div>

        <h3>Two forms.<br /><em>One quiet light.</em></h3>
        <p>
          A luminous Akoya pearl meets a delicate twin-pearl silhouette—two refined forms,
          balanced in platinum and warm champagne gold.
        </p>

        <button type="button" @click="$emit('explore', 'Rings')">
          Discover the rings
          <Icon name="arrow-right" :size="17" />
        </button>
      </article>

      <button
        class="pearl-orbit__round-link"
        type="button"
        aria-label="Explore pearl rings"
        @click="$emit('explore', 'Rings')"
      >
        <span>Explore</span>
        <Icon name="arrow-up-right" :size="22" />
      </button>

      <div class="pearl-orbit__edition" aria-label="Limited edition number 1 of 25">
        <span>Edition</span>
        <strong>01</strong>
        <i></i>
        <span>25</span>
      </div>
    </div>
  </section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz,wght@6..96,400;6..96,500&family=Manrope:wght@400;500;600&display=swap');

.pearl-orbit {
  --orbit-ivory: #fffaf2;
  --orbit-cream: #f8efe3;
  --orbit-blush: #efdeda;
  --orbit-gold: #b48a55;
  --orbit-sage: #aeb7a0;
  --orbit-taupe: #66584e;
  --orbit-ink: #453a33;
  --orbit-serif: 'Bodoni Moda', Didot, 'Bodoni MT', serif;
  --orbit-sans: 'Manrope', 'Helvetica Neue', Arial, sans-serif;
  padding: clamp(6px, 0.7vw, 13px);
  overflow: hidden;
  color: var(--orbit-ink);
  background: #dfcdbd;
}

.pearl-orbit__stage {
  position: relative;
  isolation: isolate;
  height: clamp(720px, 56.25vw, 960px);
  min-height: 720px;
  overflow: hidden;
  border: 1px solid rgba(180, 138, 85, 0.58);
  border-radius: clamp(4px, 0.75vw, 14px);
  background: var(--orbit-cream);
  box-shadow:
    0 30px 80px rgba(105, 77, 55, 0.12),
    inset 0 0 0 1px rgba(255, 255, 255, 0.54);
}

.pearl-orbit__background,
.pearl-orbit__veil {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
}

.pearl-orbit__background {
  z-index: -4;
  object-fit: cover;
  object-position: center;
  transform: scale(1.025);
  transition: transform 1.8s cubic-bezier(0.2, 0.7, 0.2, 1);
}

.pearl-orbit__veil {
  z-index: -3;
  pointer-events: none;
  background:
    linear-gradient(180deg, rgba(255, 250, 243, 0.24) 0%, transparent 24%),
    linear-gradient(90deg, rgba(248, 236, 224, 0.22), transparent 35%, transparent 68%, rgba(248, 236, 224, 0.18)),
    linear-gradient(0deg, rgba(93, 71, 55, 0.08), transparent 30%);
}

.pearl-orbit.is-visible .pearl-orbit__background {
  transform: scale(1);
}

.pearl-orbit__header {
  position: absolute;
  z-index: 7;
  top: clamp(24px, 3.2vw, 54px);
  right: clamp(24px, 4vw, 72px);
  left: clamp(24px, 4vw, 72px);
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: start;
  gap: 28px;
  font-family: var(--orbit-sans);
  color: var(--orbit-taupe);
  opacity: 0;
  transform: translateY(-12px);
  transition: opacity 0.8s ease 0.15s, transform 0.8s ease 0.15s;
}

.pearl-orbit.is-visible .pearl-orbit__header {
  opacity: 1;
  transform: translateY(0);
}

.pearl-orbit__brand,
.pearl-orbit__meta {
  display: flex;
  flex-direction: column;
  margin: 0;
  text-transform: uppercase;
}

.pearl-orbit__brand strong {
  font-family: var(--orbit-serif);
  font-size: clamp(20px, 1.7vw, 31px);
  font-weight: 500;
  line-height: 1;
  letter-spacing: 0.08em;
}

.pearl-orbit__brand span,
.pearl-orbit__meta span,
.pearl-orbit__study {
  font-size: 10px;
  font-weight: 600;
  line-height: 1.7;
  letter-spacing: 0.17em;
}

.pearl-orbit__brand span {
  margin-top: 5px;
  color: rgba(102, 88, 78, 0.72);
}

.pearl-orbit__study {
  margin: 2px 0 0;
  padding: 8px 15px;
  border: 1px solid rgba(180, 138, 85, 0.38);
  border-radius: 999px;
  background: rgba(255, 250, 242, 0.42);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  text-transform: uppercase;
}

.pearl-orbit__meta {
  align-items: flex-end;
}

.pearl-orbit__title {
  position: absolute;
  z-index: -1;
  top: 21%;
  left: 50%;
  width: 94%;
  margin: 0;
  color: rgba(255, 252, 246, 0.91);
  font-family: var(--orbit-serif);
  font-size: clamp(86px, 12.1vw, 218px);
  font-weight: 400;
  line-height: 0.72;
  letter-spacing: -0.066em;
  text-align: center;
  text-shadow: 0 2px 22px rgba(111, 82, 58, 0.15);
  transform: translate(-50%, 28px);
  opacity: 0;
  transition: opacity 1s ease 0.18s, transform 1.2s cubic-bezier(0.2, 0.75, 0.2, 1) 0.18s;
}

.pearl-orbit__title span,
.pearl-orbit__title em {
  display: block;
}

.pearl-orbit__title em {
  margin-top: 0.12em;
  color: rgba(255, 244, 224, 0.94);
  font-weight: 400;
}

.pearl-orbit.is-visible .pearl-orbit__title {
  opacity: 1;
  transform: translate(-50%, 0);
}

.pearl-orbit__jewel {
  position: absolute;
  z-index: 3;
  top: 50%;
  left: 53%;
  width: clamp(390px, 39vw, 640px);
  margin: 0;
  transform: translate(-50%, -50%) translateY(34px) scale(0.94);
  opacity: 0;
  transition: opacity 1s ease 0.32s, transform 1.35s cubic-bezier(0.16, 0.78, 0.2, 1) 0.32s;
}

.pearl-orbit.is-visible .pearl-orbit__jewel {
  opacity: 1;
  transform: translate(-50%, -50%) translateY(0) scale(1);
}

.pearl-orbit__jewel img {
  display: block;
  width: 100%;
  filter:
    drop-shadow(0 30px 32px rgba(93, 65, 41, 0.14))
    drop-shadow(0 8px 12px rgba(126, 91, 54, 0.1));
  animation: orbit-float 6s ease-in-out 1.8s infinite;
}

.pearl-orbit__jewel figcaption {
  position: absolute;
  left: 50%;
  bottom: 5%;
  width: max-content;
  max-width: 80vw;
  padding: 8px 13px;
  border: 1px solid rgba(180, 138, 85, 0.3);
  border-radius: 999px;
  color: rgba(69, 58, 51, 0.74);
  background: rgba(255, 250, 242, 0.48);
  box-shadow: 0 12px 30px rgba(87, 67, 52, 0.08);
  backdrop-filter: blur(14px);
  -webkit-backdrop-filter: blur(14px);
  font-family: var(--orbit-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  transform: translateX(-50%);
}

.pearl-orbit__story {
  position: absolute;
  z-index: 6;
  bottom: clamp(28px, 4.1vw, 70px);
  left: clamp(24px, 4vw, 72px);
  width: min(355px, calc(100% - 48px));
  padding: clamp(22px, 2.15vw, 34px);
  border: 1px solid rgba(255, 255, 255, 0.62);
  border-radius: 5px 54px 5px 5px;
  background:
    linear-gradient(135deg, rgba(255, 252, 246, 0.72), rgba(246, 232, 218, 0.46));
  box-shadow:
    0 24px 60px rgba(101, 75, 57, 0.15),
    inset 0 1px 0 rgba(255, 255, 255, 0.72);
  backdrop-filter: blur(20px) saturate(115%);
  -webkit-backdrop-filter: blur(20px) saturate(115%);
  opacity: 0;
  transform: translateY(24px);
  transition: opacity 0.85s ease 0.6s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.6s;
}

.pearl-orbit.is-visible .pearl-orbit__story {
  opacity: 1;
  transform: translateY(0);
}

.pearl-orbit__eyebrow {
  display: flex;
  align-items: center;
  gap: 10px;
  color: var(--orbit-gold);
  font-family: var(--orbit-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.pearl-orbit__eyebrow i {
  width: 28px;
  height: 1px;
  background: currentColor;
}

.pearl-orbit__story h3 {
  margin: 15px 0 12px;
  font-family: var(--orbit-serif);
  font-size: clamp(30px, 3vw, 46px);
  font-weight: 400;
  line-height: 0.98;
  letter-spacing: -0.045em;
}

.pearl-orbit__story h3 em {
  color: var(--orbit-gold);
  font-weight: 400;
}

.pearl-orbit__story > p {
  margin: 0;
  color: rgba(69, 58, 51, 0.72);
  font-family: var(--orbit-sans);
  font-size: 12px;
  line-height: 1.7;
}

.pearl-orbit__story button {
  display: inline-flex;
  align-items: center;
  gap: 11px;
  margin-top: 19px;
  padding: 0 0 7px;
  border: 0;
  border-bottom: 1px solid rgba(180, 138, 85, 0.74);
  color: var(--orbit-ink);
  background: transparent;
  font-family: var(--orbit-sans);
  font-size: 10px;
  font-weight: 600;
  letter-spacing: 0.11em;
  text-transform: uppercase;
  cursor: pointer;
  transition: color 0.25s ease, gap 0.25s ease;
}

.pearl-orbit__story button:hover {
  gap: 16px;
  color: var(--orbit-gold);
}

.pearl-orbit__story button:focus-visible,
.pearl-orbit__round-link:focus-visible {
  outline: 2px solid var(--orbit-gold);
  outline-offset: 4px;
}

.pearl-orbit__round-link {
  position: absolute;
  z-index: 6;
  right: clamp(25px, 4.1vw, 74px);
  bottom: clamp(30px, 4.6vw, 80px);
  display: grid;
  place-items: center;
  width: clamp(92px, 8vw, 126px);
  aspect-ratio: 1;
  border: 1px solid rgba(180, 138, 85, 0.5);
  border-radius: 50%;
  color: var(--orbit-taupe);
  background: rgba(255, 250, 242, 0.57);
  box-shadow: 0 20px 50px rgba(100, 75, 57, 0.13);
  backdrop-filter: blur(18px);
  -webkit-backdrop-filter: blur(18px);
  cursor: pointer;
  opacity: 0;
  transform: translateY(18px);
  transition:
    color 0.3s ease,
    background 0.3s ease,
    transform 0.85s cubic-bezier(0.2, 0.75, 0.2, 1) 0.72s,
    opacity 0.8s ease 0.72s;
}

.pearl-orbit.is-visible .pearl-orbit__round-link {
  opacity: 1;
  transform: translateY(0);
}

.pearl-orbit__round-link span {
  position: absolute;
  bottom: 22%;
  font-family: var(--orbit-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.13em;
  text-transform: uppercase;
}

.pearl-orbit__round-link :deep(svg) {
  transform: translateY(-8px);
  transition: transform 0.3s ease;
}

.pearl-orbit__round-link:hover {
  color: var(--orbit-ink);
  background: rgba(255, 250, 242, 0.82);
}

.pearl-orbit__round-link:hover :deep(svg) {
  transform: translate(4px, -12px);
}

.pearl-orbit__edition {
  position: absolute;
  z-index: 5;
  right: clamp(31px, 4.5vw, 82px);
  top: 50%;
  display: flex;
  align-items: center;
  gap: 9px;
  color: rgba(102, 88, 78, 0.7);
  font-family: var(--orbit-sans);
  font-size: 9px;
  letter-spacing: 0.14em;
  text-transform: uppercase;
  writing-mode: vertical-rl;
  transform: translateY(-50%);
}

.pearl-orbit__edition strong {
  color: var(--orbit-taupe);
  font-family: var(--orbit-serif);
  font-size: 18px;
  font-weight: 400;
  letter-spacing: 0;
}

.pearl-orbit__edition i {
  width: 1px;
  height: 28px;
  background: rgba(180, 138, 85, 0.62);
}

@keyframes orbit-float {
  0%, 100% { transform: translate3d(0, 0, 0) rotate(-0.4deg); }
  50% { transform: translate3d(0, -8px, 0) rotate(0.35deg); }
}

@media (max-width: 900px) {
  .pearl-orbit__stage {
    height: 790px;
  }

  .pearl-orbit__title {
    top: 24%;
    font-size: clamp(82px, 15.5vw, 138px);
  }

  .pearl-orbit__jewel {
    top: 46%;
    left: 53%;
    width: min(590px, 68vw);
  }

  .pearl-orbit__story {
    bottom: 35px;
  }
}

@media (max-width: 640px) {
  .pearl-orbit {
    padding: 5px;
  }

  .pearl-orbit__stage {
    height: 840px;
    min-height: 0;
    border-radius: 4px;
  }

  .pearl-orbit__background {
    object-position: 50% 50%;
  }

  .pearl-orbit__header {
    top: 24px;
    right: 20px;
    left: 20px;
    grid-template-columns: 1fr 1fr;
  }

  .pearl-orbit__study {
    display: none;
  }

  .pearl-orbit__brand strong {
    font-size: 22px;
  }

  .pearl-orbit__meta span,
  .pearl-orbit__brand span {
    font-size: 8px;
  }

  .pearl-orbit__title {
    top: 18%;
    width: calc(100% - 24px);
    font-size: clamp(69px, 22vw, 104px);
    line-height: 0.78;
  }

  .pearl-orbit__jewel {
    top: 41%;
    left: 51%;
    width: min(112vw, 520px);
  }

  .pearl-orbit__jewel figcaption {
    bottom: 1%;
    max-width: 85vw;
    overflow: hidden;
    font-size: 7px;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .pearl-orbit__story {
    right: 18px;
    bottom: 22px;
    left: 18px;
    width: auto;
    padding: 23px;
    border-radius: 4px 42px 4px 4px;
  }

  .pearl-orbit__story h3 {
    font-size: 34px;
  }

  .pearl-orbit__story > p {
    font-size: 11px;
  }

  .pearl-orbit__round-link,
  .pearl-orbit__edition {
    display: none;
  }
}

@media (prefers-reduced-motion: reduce) {
  .pearl-orbit *,
  .pearl-orbit *::before,
  .pearl-orbit *::after {
    scroll-behavior: auto !important;
    animation: none !important;
    transition-duration: 0.01ms !important;
    transition-delay: 0ms !important;
  }
}
</style>
