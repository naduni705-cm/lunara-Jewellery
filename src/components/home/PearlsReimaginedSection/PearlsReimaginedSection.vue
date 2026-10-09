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
    { threshold: 0.13 },
  )

  revealObserver.observe(section.value)
})

onBeforeUnmount(() => revealObserver?.disconnect())
</script>

<template>
  <section
    ref="section"
    class="reimagined"
    :class="{ 'is-visible': isVisible }"
    aria-labelledby="reimagined-title"
  >
    <div class="reimagined__frame">
      <header class="reimagined__topbar">
        <div class="reimagined__brand">
          <strong>Lunara</strong>
          <span>Fine pearl jewellery</span>
        </div>

        <div class="reimagined__menu" aria-hidden="true">
          <span></span><span></span><span></span>
        </div>

        <nav class="reimagined__nav" aria-label="Explore featured collections">
          <button type="button" @click="$emit('explore', 'All')">New</button>
          <button type="button" @click="$emit('explore', 'Rings')">Rings</button>
          <button type="button" @click="$emit('explore', 'Earrings')">Earrings</button>
        </nav>
      </header>

      <div class="reimagined__layout">
        <aside class="reimagined__left">
          <div class="reimagined__chapter">
            <span class="reimagined__mini-pearl" aria-hidden="true"></span>
            <strong>02.14</strong>
            <p>Chapter two<br /><span>Natural light</span></p>
          </div>

          <button
            class="reimagined__ring-card"
            type="button"
            aria-label="Explore the pearl ring edit"
            @click="$emit('explore', 'Rings')"
          >
            <span class="reimagined__ring-shadow" aria-hidden="true"></span>
            <span class="reimagined__ring-media">
              <img
                :src="asset('images/lunara-reimagined-rings.webp')"
                alt="Champagne-gold and platinum pearl rings resting on ivory silk"
                loading="lazy"
                decoding="async"
              />
            </span>
            <span class="reimagined__ring-label">
              Pairing study
              <Icon name="arrow-up-right" :size="15" />
            </span>
          </button>

          <div class="reimagined__left-copy">
            <p>
              Proportion, lustre and line—considered from every angle and finished by hand
              in small editions.
            </p>
            <button type="button" @click="$emit('explore', 'Rings')">
              Explore rings <Icon name="arrow-right" :size="16" />
            </button>
          </div>
        </aside>

        <div class="reimagined__center">
          <span class="reimagined__center-arch" aria-hidden="true"></span>

          <div class="reimagined__title-block">
            <span class="reimagined__crest" aria-hidden="true">
              <i></i>
            </span>
            <p>The modern pearl edit · No. 02</p>
            <h2 id="reimagined-title">Pearls<br /><em>reimagined.</em></h2>
          </div>

          <figure class="reimagined__portrait">
            <img
              :src="asset('images/lunara-reimagined-portrait.webp')"
              alt="Woman in profile wearing a sculptural graduated pearl earring"
              loading="lazy"
              decoding="async"
            />
            <span class="reimagined__portrait-wash" aria-hidden="true"></span>
            <figcaption>
              <span>Portrait study · No. 04</span>
              <strong>Designed around <em>your light.</em></strong>
              <button type="button" @click="$emit('explore', 'Earrings')">
                View the piece <Icon name="arrow-up-right" :size="16" />
              </button>
            </figcaption>
          </figure>
        </div>

        <aside class="reimagined__right">
          <button
            class="reimagined__earring-card"
            type="button"
            aria-label="Explore the pearl earring edit"
            @click="$emit('explore', 'Earrings')"
          >
            <span class="reimagined__earring-heading">
              <small>Atelier selection</small>
              <strong>The Aurelia drops</strong>
            </span>
            <span class="reimagined__earring-media">
              <img
                :src="asset('images/lunara-reimagined-earrings.webp')"
                alt="Pair of champagne-gold earrings with graduated white pearls"
                loading="lazy"
                decoding="async"
              />
            </span>
            <span class="reimagined__earring-arrow">
              <Icon name="arrow-up-right" :size="18" />
            </span>
          </button>

          <div class="reimagined__right-copy">
            <span>Quietly expressive</span>
            <h3>Made to move<br />with <em>you.</em></h3>
            <p>Weightless pearl forms designed to catch the light in every small movement.</p>
            <i aria-hidden="true"></i>
          </div>

          <div class="reimagined__edition" aria-hidden="true">
            <span>Small edition</span>
            <strong>01—25</strong>
          </div>
        </aside>
      </div>
    </div>
  </section>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz,wght@6..96,400;6..96,500&family=Manrope:wght@400;500;600&display=swap');

.reimagined {
  --ri-ivory: #fffaf2;
  --ri-cream: #f7eee2;
  --ri-blush: #efdcd8;
  --ri-sage: #aeb8a2;
  --ri-gold: #b58a54;
  --ri-taupe: #77685d;
  --ri-ink: #463a33;
  --ri-line: rgba(181, 138, 84, 0.42);
  --ri-serif: 'Bodoni Moda', Didot, 'Bodoni MT', serif;
  --ri-sans: 'Manrope', 'Helvetica Neue', Arial, sans-serif;
  padding: clamp(6px, 0.7vw, 13px);
  overflow: hidden;
  color: var(--ri-ink);
  background: #dfcfc1;
}

.reimagined button {
  color: inherit;
}

.reimagined__frame {
  position: relative;
  min-height: clamp(900px, 71vw, 1080px);
  overflow: hidden;
  border: 1px solid rgba(181, 138, 84, 0.64);
  border-radius: clamp(4px, 0.75vw, 14px);
  background:
    radial-gradient(circle at 50% 42%, rgba(255, 255, 255, 0.82), transparent 31%),
    linear-gradient(135deg, #fbf4ea 0%, #f1e3d8 48%, #f7efe3 100%);
  box-shadow:
    0 32px 90px rgba(93, 68, 50, 0.12),
    inset 0 0 0 1px rgba(255, 255, 255, 0.5);
}

.reimagined__frame::before {
  position: absolute;
  inset: 0;
  z-index: 0;
  background-image:
    linear-gradient(rgba(181, 138, 84, 0.045) 1px, transparent 1px),
    linear-gradient(90deg, rgba(181, 138, 84, 0.045) 1px, transparent 1px);
  background-size: 52px 52px;
  content: '';
  pointer-events: none;
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.44), transparent 76%);
}

.reimagined__topbar {
  position: relative;
  z-index: 10;
  display: grid;
  grid-template-columns: 24% 51% 25%;
  min-height: 96px;
  border-bottom: 1px solid var(--ri-line);
  font-family: var(--ri-sans);
}

.reimagined__brand,
.reimagined__nav {
  display: flex;
  align-items: center;
  padding: 22px clamp(24px, 3.5vw, 58px);
}

.reimagined__brand {
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  border-right: 1px solid var(--ri-line);
  text-transform: uppercase;
}

.reimagined__brand strong {
  font-family: var(--ri-serif);
  font-size: clamp(24px, 2vw, 34px);
  font-weight: 500;
  line-height: 1;
  letter-spacing: 0.08em;
}

.reimagined__brand span {
  margin-top: 6px;
  color: rgba(70, 58, 51, 0.62);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.18em;
}

.reimagined__menu {
  position: relative;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  justify-self: center;
  align-self: center;
  width: 76px;
  aspect-ratio: 1;
  gap: 6px;
  border: 1px solid var(--ri-line);
  border-radius: 50%;
  background: rgba(255, 250, 242, 0.32);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

.reimagined__menu::after {
  position: absolute;
  right: -5px;
  bottom: -5px;
  width: 9px;
  aspect-ratio: 1;
  border: 1px solid rgba(181, 138, 84, 0.62);
  background: var(--ri-ivory);
  content: '';
  transform: rotate(45deg);
}

.reimagined__menu span {
  display: block;
  width: 27px;
  height: 1px;
  background: var(--ri-gold);
}

.reimagined__nav {
  justify-content: flex-end;
  gap: clamp(14px, 2.1vw, 34px);
  border-left: 1px solid var(--ri-line);
}

.reimagined__nav button {
  padding: 5px 0;
  border: 0;
  border-bottom: 1px solid transparent;
  background: transparent;
  font-family: var(--ri-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.11em;
  text-transform: uppercase;
  cursor: pointer;
  transition: color 0.25s ease, border-color 0.25s ease;
}

.reimagined__nav button:hover {
  border-color: var(--ri-gold);
  color: var(--ri-gold);
}

.reimagined__layout {
  position: absolute;
  inset: 96px 0 0;
  z-index: 2;
  display: grid;
  grid-template-columns: 24% 51% 25%;
}

.reimagined__left,
.reimagined__center,
.reimagined__right {
  position: relative;
  min-width: 0;
}

.reimagined__left,
.reimagined__center {
  border-right: 1px solid var(--ri-line);
}

.reimagined__left::after,
.reimagined__right::after {
  position: absolute;
  right: 0;
  left: 0;
  top: 31%;
  height: 1px;
  background: var(--ri-line);
  content: '';
}

.reimagined__chapter {
  position: absolute;
  z-index: 3;
  top: 9%;
  right: clamp(20px, 2.4vw, 40px);
  left: clamp(20px, 2.9vw, 48px);
  display: grid;
  grid-template-columns: 34px auto;
  grid-template-rows: auto auto;
  align-items: center;
  column-gap: 14px;
  font-family: var(--ri-sans);
  opacity: 0;
  transform: translateY(-12px);
  transition: opacity 0.7s ease 0.18s, transform 0.8s ease 0.18s;
}

.reimagined.is-visible .reimagined__chapter {
  opacity: 1;
  transform: translateY(0);
}

.reimagined__mini-pearl {
  grid-row: 1 / 3;
  display: block;
  width: 30px;
  aspect-ratio: 1;
  border: 1px solid rgba(181, 138, 84, 0.4);
  border-radius: 50%;
  background: radial-gradient(circle at 34% 30%, #fff 0 11%, #f8efe3 38%, #d8c3b0 76%, #fff8ed 100%);
  box-shadow: 0 8px 20px rgba(91, 67, 50, 0.12);
}

.reimagined__chapter strong {
  font-family: var(--ri-serif);
  font-size: clamp(26px, 2.5vw, 42px);
  font-weight: 400;
  line-height: 1;
}

.reimagined__chapter p {
  margin: 4px 0 0;
  color: rgba(70, 58, 51, 0.58);
  font-size: 8px;
  font-weight: 600;
  line-height: 1.5;
  letter-spacing: 0.13em;
  text-transform: uppercase;
}

.reimagined__chapter p span {
  color: var(--ri-gold);
}

.reimagined__ring-card {
  position: absolute;
  z-index: 3;
  top: 36%;
  left: 50%;
  width: min(68%, 245px);
  padding: 0;
  border: 0;
  background: transparent;
  cursor: pointer;
  opacity: 0;
  transform: translate(-54%, 24px);
  transition: opacity 0.8s ease 0.28s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.28s;
}

.reimagined.is-visible .reimagined__ring-card {
  opacity: 1;
  transform: translate(-54%, 0);
}

.reimagined__ring-shadow,
.reimagined__ring-media {
  display: block;
  width: 100%;
  aspect-ratio: 0.72;
  border-radius: 999px;
}

.reimagined__ring-shadow {
  position: absolute;
  top: 12px;
  left: 18px;
  background: linear-gradient(160deg, rgba(174, 184, 162, 0.7), rgba(153, 137, 123, 0.5));
  box-shadow: 0 24px 48px rgba(89, 67, 51, 0.15);
}

.reimagined__ring-media {
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.74);
  box-shadow: inset 0 0 0 1px rgba(181, 138, 84, 0.18);
}

.reimagined__ring-media img,
.reimagined__earring-media img,
.reimagined__portrait > img {
  display: block;
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: transform 0.8s cubic-bezier(0.2, 0.7, 0.2, 1);
}

.reimagined__ring-media img {
  object-position: 50% 60%;
}

.reimagined__ring-label {
  position: absolute;
  right: -7px;
  bottom: 12%;
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 10px 13px;
  border: 1px solid rgba(255, 255, 255, 0.72);
  border-radius: 999px;
  color: var(--ri-taupe);
  background: rgba(255, 250, 242, 0.62);
  box-shadow: 0 13px 30px rgba(83, 63, 48, 0.11);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  font-family: var(--ri-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.reimagined__ring-card:hover .reimagined__ring-media img {
  transform: scale(1.045);
}

.reimagined__left-copy {
  position: absolute;
  right: clamp(24px, 3vw, 50px);
  bottom: 7%;
  left: clamp(24px, 3vw, 50px);
  z-index: 3;
  opacity: 0;
  transform: translateY(18px);
  transition: opacity 0.75s ease 0.62s, transform 0.85s ease 0.62s;
}

.reimagined.is-visible .reimagined__left-copy {
  opacity: 1;
  transform: translateY(0);
}

.reimagined__left-copy p {
  margin: 0;
  color: rgba(70, 58, 51, 0.68);
  font-family: var(--ri-serif);
  font-size: clamp(16px, 1.35vw, 22px);
  line-height: 1.48;
}

.reimagined__left-copy button,
.reimagined__portrait figcaption button {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  margin-top: 20px;
  padding: 0 0 7px;
  border: 0;
  border-bottom: 1px solid rgba(181, 138, 84, 0.68);
  background: transparent;
  font-family: var(--ri-sans);
  font-size: 9px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  cursor: pointer;
  transition: color 0.25s ease, gap 0.25s ease;
}

.reimagined__left-copy button:hover,
.reimagined__portrait figcaption button:hover {
  gap: 15px;
  color: var(--ri-gold);
}

.reimagined__center {
  overflow: hidden;
  background:
    radial-gradient(circle at 50% 44%, rgba(255, 255, 255, 0.66), transparent 34%),
    linear-gradient(180deg, rgba(255, 250, 242, 0.18), rgba(239, 220, 216, 0.24));
}

.reimagined__center-arch {
  position: absolute;
  top: -1px;
  left: 50%;
  z-index: 0;
  width: 86%;
  height: 48%;
  border: 1px solid var(--ri-line);
  border-bottom: 0;
  border-radius: 50% 50% 0 0 / 76% 76% 0 0;
  transform: translateX(-50%);
}

.reimagined__center-arch::after {
  position: absolute;
  right: -6px;
  bottom: -5px;
  width: 10px;
  aspect-ratio: 1;
  border: 1px solid rgba(181, 138, 84, 0.62);
  background: var(--ri-ivory);
  content: '';
  transform: rotate(45deg);
}

.reimagined__title-block {
  position: absolute;
  top: 5.5%;
  left: 50%;
  z-index: 4;
  width: 90%;
  text-align: center;
  transform: translate(-50%, 20px);
  opacity: 0;
  transition: opacity 0.95s ease 0.22s, transform 1s cubic-bezier(0.2, 0.75, 0.2, 1) 0.22s;
}

.reimagined.is-visible .reimagined__title-block {
  opacity: 1;
  transform: translate(-50%, 0);
}

.reimagined__crest {
  position: relative;
  display: inline-grid;
  place-items: center;
  width: 40px;
  aspect-ratio: 1;
  border: 1px solid rgba(181, 138, 84, 0.45);
  border-radius: 50% 50% 45% 55%;
  background: rgba(255, 250, 242, 0.38);
  transform: rotate(45deg);
}

.reimagined__crest i {
  display: block;
  width: 13px;
  aspect-ratio: 1;
  border-radius: 50%;
  background: radial-gradient(circle at 35% 28%, #fff 0 12%, #f4e7d7 42%, #cba77d 100%);
  box-shadow: 0 5px 12px rgba(105, 77, 57, 0.16);
}

.reimagined__title-block > p {
  margin: 12px 0 13px;
  color: var(--ri-gold);
  font-family: var(--ri-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.reimagined__title-block h2 {
  margin: 0;
  font-family: var(--ri-serif);
  font-size: clamp(59px, 6.2vw, 104px);
  font-weight: 400;
  line-height: 0.8;
  letter-spacing: -0.06em;
}

.reimagined__title-block h2 em {
  color: var(--ri-gold);
  font-weight: 400;
}

.reimagined__portrait {
  position: absolute;
  right: 8%;
  bottom: -1px;
  left: 8%;
  z-index: 2;
  height: 65%;
  margin: 0;
  overflow: hidden;
  border: 1px solid rgba(181, 138, 84, 0.52);
  border-bottom: 0;
  border-radius: 50% 50% 4px 4px / 24% 24% 4px 4px;
  background: var(--ri-blush);
  box-shadow: 0 30px 70px rgba(90, 66, 49, 0.14);
  opacity: 0;
  transform: translateY(46px);
  transition: opacity 0.9s ease 0.46s, transform 1.1s cubic-bezier(0.18, 0.76, 0.2, 1) 0.46s;
}

.reimagined.is-visible .reimagined__portrait {
  opacity: 1;
  transform: translateY(0);
}

.reimagined__portrait > img {
  object-position: 50% 28%;
}

.reimagined__portrait-wash {
  position: absolute;
  inset: 0;
  background:
    linear-gradient(180deg, rgba(255, 250, 242, 0.03) 52%, rgba(78, 57, 43, 0.24)),
    linear-gradient(90deg, rgba(239, 220, 216, 0.08), transparent 48%);
  pointer-events: none;
}

.reimagined__portrait figcaption {
  position: absolute;
  right: clamp(16px, 2.5vw, 38px);
  bottom: clamp(16px, 2.7vw, 40px);
  left: clamp(16px, 2.5vw, 38px);
  display: grid;
  grid-template-columns: 1fr auto;
  align-items: end;
  gap: 6px 20px;
  padding: clamp(16px, 2vw, 28px);
  border: 1px solid rgba(255, 255, 255, 0.54);
  border-radius: 4px 38px 4px 4px;
  color: var(--ri-ivory);
  background: rgba(95, 74, 61, 0.3);
  box-shadow: 0 18px 44px rgba(62, 44, 34, 0.16);
  backdrop-filter: blur(18px) saturate(110%);
  -webkit-backdrop-filter: blur(18px) saturate(110%);
}

.reimagined__portrait figcaption > span {
  grid-column: 1 / -1;
  font-family: var(--ri-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

.reimagined__portrait figcaption strong {
  font-family: var(--ri-serif);
  font-size: clamp(20px, 2vw, 31px);
  font-weight: 400;
  line-height: 1.1;
}

.reimagined__portrait figcaption strong em {
  color: #f6debd;
  font-weight: 400;
}

.reimagined__portrait figcaption button {
  margin: 0;
  border-color: rgba(255, 250, 242, 0.68);
  color: var(--ri-ivory);
}

.reimagined__right {
  padding: clamp(28px, 3.3vw, 52px);
}

.reimagined__earring-card {
  position: relative;
  z-index: 3;
  display: block;
  width: 100%;
  padding: 0;
  overflow: hidden;
  border: 1px solid var(--ri-line);
  border-radius: 50% 50% 8px 8px / 18% 18% 8px 8px;
  background: rgba(255, 250, 242, 0.46);
  box-shadow: 0 24px 58px rgba(90, 65, 48, 0.12);
  cursor: pointer;
  opacity: 0;
  transform: translateY(26px);
  transition: opacity 0.8s ease 0.36s, transform 0.95s cubic-bezier(0.2, 0.75, 0.2, 1) 0.36s;
}

.reimagined.is-visible .reimagined__earring-card {
  opacity: 1;
  transform: translateY(0);
}

.reimagined__earring-heading {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-height: 112px;
  justify-content: flex-end;
  padding: 30px 18px 18px;
  text-align: center;
}

.reimagined__earring-heading small,
.reimagined__right-copy > span {
  color: var(--ri-gold);
  font-family: var(--ri-sans);
  font-size: 8px;
  font-weight: 600;
  letter-spacing: 0.16em;
  text-transform: uppercase;
}

.reimagined__earring-heading strong {
  margin-top: 7px;
  font-family: var(--ri-serif);
  font-size: clamp(19px, 1.7vw, 27px);
  font-weight: 400;
}

.reimagined__earring-media {
  display: block;
  height: clamp(290px, 29vw, 430px);
  overflow: hidden;
  border-top: 1px solid var(--ri-line);
}

.reimagined__earring-media img {
  object-position: 50% 48%;
}

.reimagined__earring-arrow {
  position: absolute;
  right: 15px;
  bottom: 15px;
  display: grid;
  place-items: center;
  width: 46px;
  aspect-ratio: 1;
  border: 1px solid rgba(255, 255, 255, 0.66);
  border-radius: 50%;
  background: rgba(255, 250, 242, 0.62);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  transition: transform 0.28s ease, background 0.28s ease;
}

.reimagined__earring-card:hover .reimagined__earring-media img {
  transform: scale(1.045);
}

.reimagined__earring-card:hover .reimagined__earring-arrow {
  background: rgba(255, 250, 242, 0.88);
  transform: translate(3px, -3px);
}

.reimagined__right-copy {
  position: absolute;
  right: clamp(28px, 3.3vw, 52px);
  bottom: 7%;
  left: clamp(28px, 3.3vw, 52px);
  z-index: 3;
  opacity: 0;
  transform: translateY(18px);
  transition: opacity 0.75s ease 0.65s, transform 0.85s ease 0.65s;
}

.reimagined.is-visible .reimagined__right-copy {
  opacity: 1;
  transform: translateY(0);
}

.reimagined__right-copy h3 {
  margin: 11px 0 12px;
  font-family: var(--ri-serif);
  font-size: clamp(31px, 3.1vw, 49px);
  font-weight: 400;
  line-height: 0.95;
  letter-spacing: -0.045em;
}

.reimagined__right-copy h3 em {
  color: var(--ri-gold);
  font-weight: 400;
}

.reimagined__right-copy p {
  margin: 0;
  color: rgba(70, 58, 51, 0.65);
  font-family: var(--ri-sans);
  font-size: 10px;
  line-height: 1.65;
}

.reimagined__right-copy > i {
  display: block;
  width: 100%;
  height: 2px;
  margin-top: 20px;
  background: linear-gradient(90deg, var(--ri-gold), var(--ri-blush), transparent);
}

.reimagined__edition {
  position: absolute;
  right: 10px;
  bottom: 46%;
  display: flex;
  align-items: center;
  gap: 10px;
  color: rgba(70, 58, 51, 0.46);
  font-family: var(--ri-sans);
  font-size: 7px;
  letter-spacing: 0.15em;
  text-transform: uppercase;
  writing-mode: vertical-rl;
}

.reimagined__edition strong {
  color: var(--ri-gold);
  font-size: 9px;
  font-weight: 600;
}

.reimagined button:focus-visible {
  outline: 2px solid var(--ri-gold);
  outline-offset: 4px;
}

@media (max-width: 1120px) {
  .reimagined__topbar,
  .reimagined__layout {
    grid-template-columns: 26% 48% 26%;
  }

  .reimagined__left-copy p {
    font-size: 16px;
  }

  .reimagined__ring-card {
    width: 67%;
  }

  .reimagined__portrait {
    right: 5%;
    left: 5%;
  }
}

@media (max-width: 880px) {
  .reimagined__frame {
    min-height: 0;
  }

  .reimagined__topbar {
    grid-template-columns: 1fr auto;
    min-height: 86px;
  }

  .reimagined__brand {
    border-right: 0;
  }

  .reimagined__menu {
    display: none;
  }

  .reimagined__nav {
    border-left: 0;
  }

  .reimagined__layout {
    position: relative;
    inset: auto;
    display: flex;
    flex-direction: column;
  }

  .reimagined__left,
  .reimagined__center {
    border-right: 0;
  }

  .reimagined__center {
    order: 1;
    min-height: 810px;
    border-bottom: 1px solid var(--ri-line);
  }

  .reimagined__left {
    order: 2;
    min-height: 700px;
    border-bottom: 1px solid var(--ri-line);
  }

  .reimagined__right {
    order: 3;
    min-height: 850px;
  }

  .reimagined__left::after,
  .reimagined__right::after {
    top: 28%;
  }

  .reimagined__ring-card {
    top: 31%;
    width: min(250px, 55vw);
  }

  .reimagined__earring-card {
    width: min(390px, 75vw);
    margin: 0 auto;
  }

  .reimagined__earring-media {
    height: 430px;
  }

  .reimagined__right-copy {
    right: 8%;
    left: 8%;
  }
}

@media (max-width: 600px) {
  .reimagined {
    padding: 5px;
  }

  .reimagined__frame {
    border-radius: 4px;
  }

  .reimagined__topbar {
    min-height: 78px;
  }

  .reimagined__brand {
    padding: 18px;
  }

  .reimagined__brand strong {
    font-size: 23px;
  }

  .reimagined__nav {
    gap: 13px;
    padding: 18px;
  }

  .reimagined__nav button:nth-child(2) {
    display: none;
  }

  .reimagined__nav button {
    font-size: 8px;
  }

  .reimagined__center {
    min-height: 700px;
  }

  .reimagined__title-block {
    top: 6%;
  }

  .reimagined__title-block h2 {
    font-size: clamp(59px, 18vw, 79px);
  }

  .reimagined__center-arch {
    width: 96%;
    height: 43%;
  }

  .reimagined__portrait {
    right: 10px;
    left: 10px;
    height: 62%;
  }

  .reimagined__portrait figcaption {
    display: block;
    padding: 17px;
  }

  .reimagined__portrait figcaption strong {
    display: block;
    margin-top: 7px;
    font-size: 23px;
  }

  .reimagined__portrait figcaption button {
    margin-top: 13px;
  }

  .reimagined__left {
    min-height: 650px;
  }

  .reimagined__chapter {
    right: 20px;
    left: 20px;
  }

  .reimagined__ring-card {
    width: min(220px, 57vw);
  }

  .reimagined__left-copy {
    right: 24px;
    bottom: 5%;
    left: 24px;
  }

  .reimagined__right {
    min-height: 765px;
    padding: 24px;
  }

  .reimagined__earring-card {
    width: min(330px, 100%);
  }

  .reimagined__earring-media {
    height: 360px;
  }

  .reimagined__right-copy {
    right: 24px;
    bottom: 5%;
    left: 24px;
  }

  .reimagined__right-copy h3 {
    font-size: 39px;
  }

  .reimagined__edition {
    display: none;
  }
}

@media (prefers-reduced-motion: reduce) {
  .reimagined *,
  .reimagined *::before,
  .reimagined *::after {
    scroll-behavior: auto !important;
    transition-duration: 0.01ms !important;
    transition-delay: 0ms !important;
  }
}
</style>
