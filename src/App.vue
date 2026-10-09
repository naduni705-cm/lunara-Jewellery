<script setup>
import { computed, nextTick, onBeforeUnmount, onMounted, reactive, ref, watch } from 'vue'
import HomePage from './pages/HomePage/HomePage.vue'
import Icon from './components/shared/Icon/Icon.vue'
import ProductCard from './components/shared/ProductCard/ProductCard.vue'
import { collections, journalArticles, products } from './data/catalog.js'

const asset = (path) => `${import.meta.env.BASE_URL}${path}`
const money = (value) => new Intl.NumberFormat('en-US', {
  style: 'currency',
  currency: 'USD',
  maximumFractionDigits: 0,
}).format(value)

const routePath = ref('/')
const mobileOpen = ref(false)
const searchOpen = ref(false)
const cartOpen = ref(false)
const checkoutOpen = ref(false)
const quickProduct = ref(null)
const searchTerm = ref('')
const toastMessage = ref('')
const scrollProgress = ref(0)
const reducedMotion = ref(false)
let toastTimer
let webMcpLifecycle
let revealObserver
let scrollFrame

const parseStored = (key, fallback) => {
  try {
    return JSON.parse(localStorage.getItem(key)) ?? fallback
  } catch {
    return fallback
  }
}

const cart = ref(parseStored('lunara-cart', []))
const wishlist = ref(parseStored('lunara-wishlist', []))
const shopCategory = ref('All')
const shopSort = ref('featured')
const productQty = ref(1)
const openCare = ref(0)
const contactSent = ref(false)
const orderPlaced = ref(false)
const orderNumber = ref('')

const contactForm = reactive({ name: '', email: '', subject: 'Product enquiry', message: '' })
const checkoutForm = reactive({ firstName: '', lastName: '', email: '', country: '', address: '', city: '', postal: '' })

const page = computed(() => routePath.value.split('/').filter(Boolean)[0] || 'home')
const routeProduct = computed(() => {
  if (page.value !== 'product') return null
  const id = routePath.value.split('/').filter(Boolean)[1]
  return products.find((product) => product.id === id) || null
})

const cartCount = computed(() => cart.value.reduce((total, item) => total + item.quantity, 0))
const cartSubtotal = computed(() => cart.value.reduce((total, item) => {
  const product = products.find((entry) => entry.id === item.id)
  return total + (product?.price || 0) * item.quantity
}, 0))
const cartItems = computed(() => cart.value.map((item) => ({
  ...products.find((product) => product.id === item.id),
  quantity: item.quantity,
})).filter((item) => item.id))

const filteredProducts = computed(() => {
  const selection = shopCategory.value === 'All'
    ? [...products]
    : shopCategory.value === 'Saved'
      ? products.filter((product) => wishlist.value.includes(product.id))
      : products.filter((product) => product.category === shopCategory.value)

  if (shopSort.value === 'low') return selection.sort((a, b) => a.price - b.price)
  if (shopSort.value === 'high') return selection.sort((a, b) => b.price - a.price)
  if (shopSort.value === 'new') return selection.sort((a, b) => Number(b.badge === 'New') - Number(a.badge === 'New'))
  return selection
})

const searchResults = computed(() => {
  const term = searchTerm.value.trim().toLowerCase()
  if (!term) return products.slice(0, 4)
  return products.filter((product) =>
    [product.name, product.category, product.pearl].some((value) => value.toLowerCase().includes(term)),
  )
})

const relatedProducts = computed(() => {
  if (!routeProduct.value) return products.slice(0, 4)
  const related = products.filter((product) => product.id !== routeProduct.value.id && product.category === routeProduct.value.category)
  return [...related, ...products.filter((product) => product.id !== routeProduct.value.id && !related.includes(product))].slice(0, 4)
})

const navItems = [
  { label: 'New arrivals', path: '/shop' },
  { label: 'Collections', path: '/collections' },
  { label: 'Our story', path: '/story' },
  { label: 'Journal', path: '/journal' },
]

const careItems = [
  { title: 'How should I store my pearls?', body: 'Keep pearls flat in their soft pouch, separate from harder jewellery. Avoid airtight containers—pearls appreciate a little natural humidity.' },
  { title: 'Can I wear them every day?', body: 'Absolutely. Put pearls on last, after fragrance and skincare, and gently wipe them with the included cloth before returning them to their pouch.' },
  { title: 'Why do organic pearls vary?', body: 'Each pearl grows naturally and carries its own shape, tone and surface character. These small differences are part of the piece, not imperfections.' },
  { title: 'Do you offer repairs?', body: 'Yes. Lunara pieces include a two-year care promise, and our atelier can restring or repair your jewellery beyond that period for a considered fee.' },
]

function updateScrollProgress() {
  const height = document.documentElement.scrollHeight - window.innerHeight
  scrollProgress.value = height > 0 ? Math.min(100, (window.scrollY / height) * 100) : 0
}

function handleScroll() {
  window.cancelAnimationFrame(scrollFrame)
  scrollFrame = window.requestAnimationFrame(updateScrollProgress)
}

function initRevealAnimations() {
  revealObserver?.disconnect()
  nextTick(() => {
    const elements = [...document.querySelectorAll('.reveal-on-scroll')]
    if (reducedMotion.value || !('IntersectionObserver' in window)) {
      elements.forEach((element) => element.classList.add('is-visible'))
      return
    }
    revealObserver = new IntersectionObserver((entries) => {
      entries.forEach((entry) => {
        if (!entry.isIntersecting) return
        entry.target.classList.add('is-visible')
        revealObserver.unobserve(entry.target)
      })
    }, { threshold: 0.12, rootMargin: '0px 0px -7% 0px' })
    elements.forEach((element) => revealObserver.observe(element))
  })
}

function readRoute() {
  routePath.value = window.location.hash.replace(/^#/, '').split('?')[0] || '/'
  mobileOpen.value = false
  searchOpen.value = false
  quickProduct.value = null
  productQty.value = 1
  window.scrollTo({ top: 0, behavior: 'smooth' })
}

function navigate(path) {
  if (routePath.value === path) {
    window.scrollTo({ top: 0, behavior: 'smooth' })
    mobileOpen.value = false
    return
  }
  window.location.hash = path
}

function openShop(category = 'All') {
  shopCategory.value = category
  navigate('/shop')
}

function openWishlist() {
  shopCategory.value = 'Saved'
  navigate('/shop')
}

function notify(message) {
  toastMessage.value = message
  window.clearTimeout(toastTimer)
  toastTimer = window.setTimeout(() => { toastMessage.value = '' }, 2800)
}

function addToCart(product, quantity = 1) {
  const safeQuantity = Math.max(1, Math.min(9, Number(quantity) || 1))
  const existing = cart.value.find((item) => item.id === product.id)
  if (existing) existing.quantity = Math.min(9, existing.quantity + safeQuantity)
  else cart.value.push({ id: product.id, quantity: safeQuantity })
  notify(`${product.name} added to your bag`)
}

function updateQuantity(id, difference) {
  const item = cart.value.find((entry) => entry.id === id)
  if (!item) return
  item.quantity += difference
  if (item.quantity <= 0) cart.value = cart.value.filter((entry) => entry.id !== id)
}

function removeFromCart(id) {
  cart.value = cart.value.filter((item) => item.id !== id)
}

function toggleWish(product) {
  wishlist.value = wishlist.value.includes(product.id)
    ? wishlist.value.filter((id) => id !== product.id)
    : [...wishlist.value, product.id]
  notify(wishlist.value.includes(product.id) ? 'Saved to your wishlist' : 'Removed from your wishlist')
}

function openQuick(product) {
  quickProduct.value = product
  productQty.value = 1
}

function openFullProduct(product) {
  quickProduct.value = null
  navigate(`/product/${product.id}`)
}

function submitContact() {
  contactSent.value = true
}

function beginCheckout() {
  if (!cart.value.length) return
  cartOpen.value = false
  checkoutOpen.value = true
  orderPlaced.value = false
}

function placeOrder() {
  orderNumber.value = `LN-${String(Date.now()).slice(-6)}`
  orderPlaced.value = true
  cart.value = []
}

function closeCheckout() {
  checkoutOpen.value = false
  if (orderPlaced.value) navigate('/')
}

function handleEscape(event) {
  if (event.key !== 'Escape') return
  mobileOpen.value = false
  searchOpen.value = false
  cartOpen.value = false
  checkoutOpen.value = false
  quickProduct.value = null
}

function registerWebMcpTools() {
  const context = document.modelContext
  if (!context?.registerTool) return
  webMcpLifecycle = new AbortController()
  const options = { signal: webMcpLifecycle.signal }
  const register = (tool) => {
    try { void Promise.resolve(context.registerTool(tool, options)).catch(() => {}) } catch { /* unsupported preview */ }
  }

  register({
    name: 'list_pearl_products',
    title: 'List pearl jewellery',
    description: 'List the pearl jewellery available in the Lunara boutique, optionally filtered by category.',
    inputSchema: {
      type: 'object',
      properties: { category: { type: 'string', enum: ['All', 'Necklaces', 'Earrings', 'Rings', 'Bracelets'] } },
      additionalProperties: false,
    },
    annotations: { readOnlyHint: true, untrustedContentHint: false },
    execute(input = {}) {
      const category = input.category || 'All'
      const result = category === 'All' ? products : products.filter((product) => product.category === category)
      return { products: result.map(({ id, name, category: type, price }) => ({ id, name, category: type, price, currency: 'USD' })) }
    },
  })

  register({
    name: 'add_pearl_jewellery_to_bag',
    title: 'Add jewellery to bag',
    description: 'Add a Lunara product to the same shopping bag shown in the storefront.',
    inputSchema: {
      type: 'object',
      properties: {
        productId: { type: 'string' },
        quantity: { type: 'integer', minimum: 1, maximum: 9 },
      },
      required: ['productId'],
      additionalProperties: false,
    },
    annotations: { readOnlyHint: false, untrustedContentHint: false },
    execute(input) {
      const product = products.find((entry) => entry.id === input?.productId)
      if (!product) throw new Error('Unknown productId')
      addToCart(product, input.quantity || 1)
      return { added: product.id, quantity: input.quantity || 1, bagItemCount: cartCount.value }
    },
  })
}

watch(cart, (value) => localStorage.setItem('lunara-cart', JSON.stringify(value)), { deep: true })
watch(wishlist, (value) => localStorage.setItem('lunara-wishlist', JSON.stringify(value)), { deep: true })
watch(
  () => [mobileOpen.value, searchOpen.value, cartOpen.value, checkoutOpen.value, Boolean(quickProduct.value)],
  (states) => document.body.classList.toggle('is-locked', states.some(Boolean)),
)
watch([page, routeProduct], () => {
  const title = routeProduct.value?.name || ({ home: 'Modern Pearl Jewellery', shop: 'Shop Pearls', collections: 'Collections', story: 'Our Story', journal: 'Journal', contact: 'Contact' }[page.value] || 'Pearl Jewellery')
  document.title = `${title} — Lunara`
  initRevealAnimations()
})

onMounted(() => {
  reducedMotion.value = window.matchMedia('(prefers-reduced-motion: reduce)').matches
  readRoute()
  window.addEventListener('hashchange', readRoute)
  window.addEventListener('keydown', handleEscape)
  window.addEventListener('scroll', handleScroll, { passive: true })
  updateScrollProgress()
  initRevealAnimations()
  registerWebMcpTools()
})

onBeforeUnmount(() => {
  window.removeEventListener('hashchange', readRoute)
  window.removeEventListener('keydown', handleEscape)
  window.removeEventListener('scroll', handleScroll)
  window.cancelAnimationFrame(scrollFrame)
  revealObserver?.disconnect()
  window.clearTimeout(toastTimer)
  webMcpLifecycle?.abort()
  document.body.classList.remove('is-locked')
})
</script>

<template>
  <div class="site-shell">
    <div class="scroll-progress" aria-hidden="true"><span :style="{ width: `${scrollProgress}%` }"></span></div>
    <div class="announcement">
      <p>Complimentary worldwide delivery on orders over $250</p>
      <span>Responsibly sourced · Hand-finished</span>
    </div>

    <header class="site-header" :class="{ 'site-header--overlay': page === 'home' }">
      <div class="header-inner">
        <button class="icon-button mobile-menu-button" type="button" aria-label="Open menu" @click="mobileOpen = true">
          <Icon name="menu" :size="21" />
        </button>

        <nav class="desktop-nav" aria-label="Primary navigation">
          <a
            v-for="item in navItems.slice(0, 2)"
            :key="item.path"
            :href="`#${item.path}`"
            :class="{ active: routePath === item.path }"
            @click.prevent="navigate(item.path)"
          >{{ item.label }}</a>
        </nav>

        <button class="brand" type="button" aria-label="Lunara home" @click="navigate('/')">
          <span class="brand-mark">L</span>
          <span class="brand-name">LUNARA</span>
          <span class="brand-sub">Fine pearls</span>
        </button>

        <nav class="desktop-nav desktop-nav--right" aria-label="Secondary navigation">
          <a
            v-for="item in navItems.slice(2)"
            :key="item.path"
            :href="`#${item.path}`"
            :class="{ active: routePath === item.path }"
            @click.prevent="navigate(item.path)"
          >{{ item.label }}</a>
        </nav>

        <div class="header-actions">
          <button class="icon-button" type="button" aria-label="Search" @click="searchOpen = true">
            <Icon name="search" :size="19" />
          </button>
          <button class="icon-button wishlist-header" type="button" aria-label="View saved jewellery" @click="openWishlist">
            <Icon name="heart" :size="19" />
            <span v-if="wishlist.length" class="action-count">{{ wishlist.length }}</span>
          </button>
          <button class="bag-button" type="button" aria-label="Open shopping bag" @click="cartOpen = true">
            <Icon name="bag" :size="18" />
            <span>Bag</span>
            <span class="bag-count">{{ cartCount }}</span>
          </button>
        </div>
      </div>
    </header>

    <main>
      <template v-if="page === 'home'">
        <HomePage
          :wishlist="wishlist"
          @open-shop="openShop"
          @navigate="navigate"
          @add-to-cart="addToCart"
          @toggle-wish="toggleWish"
          @open-quick="openQuick"
          @open-product="openFullProduct"
        />
      </template>

      <template v-else-if="page === 'shop'">
        <section class="page-masthead page-masthead--shop">
          <p class="eyebrow">The complete edit</p>
          <h1>Shop pearl jewellery</h1>
          <p>Modern heirlooms shaped by natural beauty and the hands that finish them.</p>
        </section>
        <section class="shop-section container">
          <div class="shop-toolbar">
            <div class="filter-pills" role="group" aria-label="Filter products by category">
              <button
                v-for="category in ['All', 'Necklaces', 'Earrings', 'Rings', 'Bracelets', ...(wishlist.length ? ['Saved'] : [])]"
                :key="category"
                type="button"
                :class="{ active: shopCategory === category }"
                @click="shopCategory = category"
              >{{ category }}</button>
            </div>
            <label class="sort-select">Sort
              <select v-model="shopSort" aria-label="Sort jewellery">
                <option value="featured">Featured</option>
                <option value="new">Newest</option>
                <option value="low">Price, low to high</option>
                <option value="high">Price, high to low</option>
              </select>
              <Icon name="chevron-down" :size="15" />
            </label>
          </div>
          <div class="shop-count"><span>{{ filteredProducts.length }} pieces</span><span>Designed in small editions</span></div>
          <div v-if="!filteredProducts.length" class="empty-products">
            <span class="empty-bag__pearl"></span>
            <h2>No saved pieces yet.</h2>
            <p>Tap the heart on any piece to keep it close.</p>
            <button class="button button--outline" type="button" @click="shopCategory = 'All'">Explore all jewellery</button>
          </div>
          <div v-else class="product-grid product-grid--shop">
            <ProductCard
              v-for="product in filteredProducts"
              :key="product.id"
              :product="product"
              :wished="wishlist.includes(product.id)"
              @add="addToCart"
              @wish="toggleWish"
              @open="openQuick"
            />
          </div>
        </section>
        <section class="material-note">
          <div class="material-note__number">L / 01</div>
          <div><p class="eyebrow">Our materials</p><h2>Considered from pearl to clasp.</h2></div>
          <p>We select freshwater pearls for lustre and character, use recycled sterling silver where possible, and finish in a warm layer of 18k gold vermeil.</p>
          <button class="text-link" type="button" @click="navigate('/story')">Read our standards <Icon name="arrow-right" :size="16" /></button>
        </section>
      </template>

      <template v-else-if="page === 'collections'">
        <section class="page-masthead collections-masthead">
          <p class="eyebrow">Lunara collections</p>
          <h1>Forms of light</h1>
          <p>Three considered expressions of the pearl—from the eternal icon to the beautifully unexpected.</p>
        </section>
        <section class="collection-editorial container">
          <article v-for="(collection, index) in collections" :key="collection.name" class="collection-row" :class="{ reverse: index % 2 }">
            <div class="collection-row__image"><img :src="asset(collection.image)" :alt="collection.name" loading="lazy" /><span>{{ String(index + 1).padStart(2, '0') }}</span></div>
            <div class="collection-row__copy">
              <p class="eyebrow">{{ collection.kicker }}</p>
              <h2>{{ collection.name }}</h2>
              <p>{{ collection.copy }}</p>
              <button class="button button--outline" type="button" @click="openShop(collection.category)">View the collection <Icon name="arrow-right" :size="16" /></button>
            </div>
          </article>
        </section>
      </template>

      <template v-else-if="page === 'story'">
        <section class="story-hero">
          <div class="story-hero__copy">
            <p class="eyebrow">Our story</p>
            <h1>Jewellery with a<br /><em>quieter voice.</em></h1>
            <p>Lunara began with a belief: true luxury is not loud. It is found in natural materials, considered proportions and the care of a human hand.</p>
          </div>
          <div class="story-hero__image"><img :src="asset('images/atelier-craft.webp')" alt="Lunara artisan finishing pearl jewellery" /></div>
        </section>
        <section class="story-manifesto container">
          <p class="manifesto-lead">“We design around the pearl—not over it—letting its individual light lead every decision.”</p>
          <div class="manifesto-copy">
            <p>Our pieces begin with freshwater pearls selected one by one for their lustre, tone and character. Some are serenely round; others are rippled and wonderfully irregular. We honour both.</p>
            <p>From there, each design is refined to feel effortless on the body. Our small-edition approach gives the atelier time to match, string, knot and finish every piece with close attention.</p>
          </div>
        </section>
        <section class="values-section">
          <div class="section-heading section-heading--center"><p class="eyebrow">The Lunara standard</p><h2>Luxury, considered</h2></div>
          <div class="values-grid container">
            <article><span>01</span><h3>Natural character</h3><p>We embrace the shape, tiny ridges and nuanced glow that make each pearl individual.</p></article>
            <article><span>02</span><h3>Small editions</h3><p>Thoughtful quantities create less waste and leave more room for the hand of the maker.</p></article>
            <article><span>03</span><h3>Lasting care</h3><p>Every piece is made to be worn, maintained and repaired—not treated as disposable.</p></article>
          </div>
        </section>
        <section class="story-signoff">
          <img :src="asset('images/hero-pearl-editorial.webp')" alt="Lunara pearl necklace campaign" loading="lazy" />
          <div><p class="eyebrow">Made for your story</p><h2>Wear it now.<br />Pass it on later.</h2><button class="button button--ivory" type="button" @click="openShop('All')">Explore all pieces <Icon name="arrow-right" :size="17" /></button></div>
        </section>
      </template>

      <template v-else-if="page === 'journal'">
        <section class="page-masthead journal-masthead">
          <p class="eyebrow">The Lunara journal</p>
          <h1>Stories of craft,<br /><em>care & character.</em></h1>
        </section>
        <section class="journal-page container">
          <article class="journal-feature">
            <div class="journal-feature__image"><img :src="asset(journalArticles[1].image)" :alt="journalArticles[1].title" /></div>
            <div><p class="eyebrow">Featured · {{ journalArticles[1].tag }}</p><h2>{{ journalArticles[1].title }}</h2><p>{{ journalArticles[1].excerpt }}</p><button class="text-link" type="button">Read the story <Icon name="arrow-right" :size="16" /></button></div>
          </article>
          <div class="journal-grid journal-grid--page">
            <article v-for="article in journalArticles" :key="article.title" class="journal-card">
              <div class="journal-card__image"><img :src="asset(article.image)" :alt="article.title" loading="lazy" /></div>
              <p class="eyebrow">{{ article.tag }}</p><h3>{{ article.title }}</h3><p>{{ article.excerpt }}</p>
              <div class="journal-card__meta"><span>{{ article.date }}</span><span>{{ article.read }}</span></div>
            </article>
          </div>
        </section>
        <section class="care-guide">
          <div class="care-guide__intro"><p class="eyebrow">Pearl care, simply</p><h2>Keep their glow</h2><p>Pearls are wonderfully wearable. A few gentle habits are all they need to stay luminous.</p></div>
          <div class="accordion-list">
            <article v-for="(item, index) in careItems" :key="item.title" :class="{ open: openCare === index }">
              <button type="button" @click="openCare = openCare === index ? -1 : index"><span>{{ String(index + 1).padStart(2, '0') }} · {{ item.title }}</span><Icon :name="openCare === index ? 'minus' : 'plus'" :size="18" /></button>
              <div class="accordion-answer"><p>{{ item.body }}</p></div>
            </article>
          </div>
        </section>
      </template>

      <template v-else-if="page === 'contact'">
        <section class="contact-page">
          <div class="contact-intro">
            <p class="eyebrow">We are here</p>
            <h1>Let us help you<br /><em>find the one.</em></h1>
            <p>Questions about a pearl, a gift, sizing or care? Our boutique team will reply within one business day.</p>
            <div class="contact-details">
              <div><span>Email</span><a href="mailto:hello@lunara.example">hello@lunara.example</a></div>
              <div><span>Boutique hours</span><p>Monday–Friday, 9:00–18:00 UTC</p></div>
              <div><span>Worldwide</span><p>Virtual styling appointments available</p></div>
            </div>
          </div>
          <div class="contact-form-wrap">
            <form v-if="!contactSent" class="contact-form" @submit.prevent="submitContact">
              <div class="field-row"><label>Full name<input v-model="contactForm.name" type="text" required placeholder="Your name" /></label><label>Email address<input v-model="contactForm.email" type="email" required placeholder="you@example.com" /></label></div>
              <label>How can we help?<select v-model="contactForm.subject"><option>Product enquiry</option><option>Order support</option><option>Gifting and styling</option><option>Care and repair</option><option>Press enquiry</option></select></label>
              <label>Your message<textarea v-model="contactForm.message" rows="6" required placeholder="Tell us what you would like to know..."></textarea></label>
              <button class="button button--gold" type="submit">Send your message <Icon name="arrow-right" :size="17" /></button>
            </form>
            <div v-else class="contact-success"><span class="success-pearl"><Icon name="check" :size="28" /></span><p class="eyebrow">Message received</p><h2>Thank you, {{ contactForm.name }}.</h2><p>Our boutique team will be in touch within one business day.</p><button class="text-link" type="button" @click="contactSent = false">Send another message</button></div>
          </div>
        </section>
      </template>

      <template v-else-if="page === 'product' && routeProduct">
        <section class="product-page container">
          <nav class="breadcrumbs" aria-label="Breadcrumb"><button type="button" @click="navigate('/')">Home</button><span>/</span><button type="button" @click="openShop(routeProduct.category)">{{ routeProduct.category }}</button><span>/</span><span>{{ routeProduct.name }}</span></nav>
          <div class="product-layout">
            <div class="product-gallery">
              <div class="product-main-image"><img :src="asset(routeProduct.image)" :alt="routeProduct.name" /></div>
              <div class="product-detail-image"><img :src="asset('images/atelier-craft.webp')" alt="Pearl jewellery hand-finished in the Lunara atelier" /></div>
            </div>
            <div class="product-info">
              <p class="eyebrow">{{ routeProduct.badge || routeProduct.category }}</p>
              <h1>{{ routeProduct.name }}</h1>
              <p class="product-price">{{ money(routeProduct.price) }}</p>
              <p class="product-description">{{ routeProduct.description }}</p>
              <div class="product-specs"><p><span>Pearl</span>{{ routeProduct.pearl }}</p><p><span>Finish</span>{{ routeProduct.finish }}</p><p><span>Size</span>{{ routeProduct.size }}</p></div>
              <div class="product-purchase">
                <div class="quantity-picker"><button type="button" aria-label="Decrease quantity" @click="productQty = Math.max(1, productQty - 1)"><Icon name="minus" :size="15" /></button><span>{{ productQty }}</span><button type="button" aria-label="Increase quantity" @click="productQty = Math.min(9, productQty + 1)"><Icon name="plus" :size="15" /></button></div>
                <button class="button button--gold product-add-button" type="button" @click="addToCart(routeProduct, productQty)">Add to bag · {{ money(routeProduct.price * productQty) }}</button>
              </div>
              <button class="wishlist-line" type="button" @click="toggleWish(routeProduct)"><Icon name="heart" :size="18" /> {{ wishlist.includes(routeProduct.id) ? 'Saved to your wishlist' : 'Save to your wishlist' }}</button>
              <div class="detail-accordions"><details open><summary>Details & craftsmanship <Icon name="chevron-down" :size="16" /></summary><p>Hand-finished in small editions. Natural variations in tone and surface make your piece beautifully individual.</p></details><details><summary>Delivery & returns <Icon name="chevron-down" :size="16" /></summary><p>Complimentary worldwide delivery over $250. Returns are accepted within 30 days in original condition.</p></details><details><summary>Care <Icon name="chevron-down" :size="16" /></summary><p>Wear often, avoid direct contact with fragrance, and wipe gently with the included soft cloth after use.</p></details></div>
              <div class="gift-note"><span class="tiny-pearl"></span><p><strong>Beautifully presented</strong>Every order arrives in our signature ivory keepsake box with a handwritten gift note on request.</p></div>
            </div>
          </div>
        </section>
        <section class="section related-section container"><div class="section-heading section-heading--row"><div><p class="eyebrow">You may also love</p><h2>Continue the story</h2></div></div><div class="product-grid"><ProductCard v-for="product in relatedProducts" :key="product.id" :product="product" :wished="wishlist.includes(product.id)" @add="addToCart" @wish="toggleWish" @open="openQuick" /></div></section>
      </template>

      <section v-else class="not-found"><p class="eyebrow">404</p><h1>This pearl has wandered.</h1><button class="button button--gold" type="button" @click="navigate('/')">Return home</button></section>
    </main>

    <footer class="site-footer">
      <div class="footer-top container">
        <div class="footer-brand"><button class="brand brand--footer" type="button" @click="navigate('/')"><span class="brand-mark">L</span><span class="brand-name">LUNARA</span><span class="brand-sub">Fine pearls</span></button><p>Modern pearl jewellery with natural character, hand-finished in considered editions.</p><div class="footer-socials"><a href="#" aria-label="Instagram">IG</a><a href="#" aria-label="Pinterest">PI</a><a href="#" aria-label="TikTok">TK</a></div></div>
        <div class="footer-links"><div><h3>Discover</h3><button type="button" @click="openShop('All')">Shop all</button><button type="button" @click="navigate('/collections')">Collections</button><button type="button" @click="navigate('/journal')">Journal</button></div><div><h3>About</h3><button type="button" @click="navigate('/story')">Our story</button><button type="button" @click="navigate('/story')">Materials</button><button type="button" @click="navigate('/contact')">Contact</button></div><div><h3>Client care</h3><button type="button" @click="navigate('/journal')">Pearl care</button><button type="button" @click="navigate('/contact')">Delivery & returns</button><button type="button" @click="navigate('/contact')">Repairs</button></div></div>
      </div>
      <div class="footer-bottom container"><span>© 2026 Lunara Pearl Jewellery</span><span>USD · English</span><div><a href="#">Privacy</a><a href="#">Terms</a></div></div>
    </footer>

    <Transition name="fade">
      <div v-if="mobileOpen" class="overlay" @click.self="mobileOpen = false">
        <aside class="mobile-drawer" aria-label="Mobile navigation">
          <div class="drawer-header"><span>Menu</span><button class="icon-button" type="button" aria-label="Close menu" @click="mobileOpen = false"><Icon name="close" /></button></div>
          <nav><a href="#/shop" @click.prevent="navigate('/shop')">New arrivals <Icon name="arrow-up-right" :size="18" /></a><a href="#/collections" @click.prevent="navigate('/collections')">Collections <Icon name="arrow-up-right" :size="18" /></a><a href="#/story" @click.prevent="navigate('/story')">Our story <Icon name="arrow-up-right" :size="18" /></a><a href="#/journal" @click.prevent="navigate('/journal')">Journal <Icon name="arrow-up-right" :size="18" /></a><a href="#/contact" @click.prevent="navigate('/contact')">Contact <Icon name="arrow-up-right" :size="18" /></a></nav>
          <div class="mobile-drawer__foot"><p>Complimentary worldwide delivery over $250.</p><span>USD · English</span></div>
        </aside>
      </div>
    </Transition>

    <Transition name="fade">
      <div v-if="searchOpen" class="search-overlay" role="dialog" aria-modal="true" aria-label="Search jewellery">
        <button class="icon-button search-close" type="button" aria-label="Close search" @click="searchOpen = false"><Icon name="close" :size="22" /></button>
        <div class="search-panel">
          <p class="eyebrow">Find your piece</p>
          <label class="search-input-wrap"><Icon name="search" :size="25" /><input v-model="searchTerm" type="search" placeholder="Search pearls, necklaces, earrings..." autofocus /></label>
          <div class="search-results"><p>{{ searchTerm ? `${searchResults.length} results` : 'Popular now' }}</p><button v-for="product in searchResults" :key="product.id" type="button" @click="openFullProduct(product)"><img :src="asset(product.image)" :alt="product.name" /><span><strong>{{ product.name }}</strong><small>{{ product.category }} · {{ money(product.price) }}</small></span><Icon name="arrow-right" :size="16" /></button></div>
        </div>
      </div>
    </Transition>

    <Transition name="fade">
      <div v-if="cartOpen" class="overlay" @click.self="cartOpen = false">
        <aside class="cart-drawer" role="dialog" aria-modal="true" aria-label="Shopping bag">
          <div class="drawer-header"><div><span>Your bag</span><small>{{ cartCount }} {{ cartCount === 1 ? 'piece' : 'pieces' }}</small></div><button class="icon-button" type="button" aria-label="Close bag" @click="cartOpen = false"><Icon name="close" /></button></div>
          <div v-if="cartItems.length" class="cart-content">
            <div class="shipping-progress"><p v-if="cartSubtotal < 250">You are {{ money(250 - cartSubtotal) }} away from complimentary delivery.</p><p v-else>Complimentary worldwide delivery is yours.</p><div><span :style="{ width: `${Math.min(100, cartSubtotal / 250 * 100)}%` }"></span></div></div>
            <div class="cart-items"><article v-for="item in cartItems" :key="item.id"><img :src="asset(item.image)" :alt="item.name" /><div><button class="cart-item-title" type="button" @click="openFullProduct(item); cartOpen = false">{{ item.name }}</button><p>{{ item.finish }}</p><span>{{ money(item.price) }}</span><div class="cart-item-actions"><div class="quantity-picker quantity-picker--small"><button type="button" aria-label="Decrease quantity" @click="updateQuantity(item.id, -1)"><Icon name="minus" :size="13" /></button><span>{{ item.quantity }}</span><button type="button" aria-label="Increase quantity" @click="updateQuantity(item.id, 1)"><Icon name="plus" :size="13" /></button></div><button class="remove-link" type="button" @click="removeFromCart(item.id)">Remove</button></div></div></article></div>
            <div class="cart-summary"><div><span>Subtotal</span><strong>{{ money(cartSubtotal) }}</strong></div><p>Taxes and delivery calculated at checkout.</p><button class="button button--gold" type="button" @click="beginCheckout">Continue to checkout <Icon name="arrow-right" :size="17" /></button><button class="button button--text" type="button" @click="cartOpen = false; openShop('All')">Continue shopping</button></div>
          </div>
          <div v-else class="empty-bag"><span class="empty-bag__pearl"></span><p class="eyebrow">Your bag is waiting</p><h2>Find something luminous.</h2><p>Explore pieces made to be worn today and treasured long after.</p><button class="button button--gold" type="button" @click="cartOpen = false; openShop('All')">Explore the collection</button></div>
        </aside>
      </div>
    </Transition>

    <Transition name="fade">
      <div v-if="quickProduct" class="modal-overlay" @click.self="quickProduct = null">
        <section class="quick-modal" role="dialog" aria-modal="true" :aria-label="`Quick view ${quickProduct.name}`">
          <button class="icon-button modal-close" type="button" aria-label="Close quick view" @click="quickProduct = null"><Icon name="close" /></button>
          <div class="quick-modal__image"><img :src="asset(quickProduct.image)" :alt="quickProduct.name" /></div>
          <div class="quick-modal__copy"><p class="eyebrow">{{ quickProduct.badge || quickProduct.category }}</p><h2>{{ quickProduct.name }}</h2><p class="product-price">{{ money(quickProduct.price) }}</p><p>{{ quickProduct.description }}</p><div class="product-specs"><p><span>Pearl</span>{{ quickProduct.pearl }}</p><p><span>Finish</span>{{ quickProduct.finish }}</p></div><button class="button button--gold" type="button" @click="addToCart(quickProduct); quickProduct = null">Add to bag</button><button class="text-link" type="button" @click="openFullProduct(quickProduct)">View full details <Icon name="arrow-right" :size="16" /></button></div>
        </section>
      </div>
    </Transition>

    <Transition name="fade">
      <div v-if="checkoutOpen" class="checkout-overlay">
        <section class="checkout-modal" role="dialog" aria-modal="true" aria-label="Checkout">
          <button class="icon-button modal-close" type="button" aria-label="Close checkout" @click="closeCheckout"><Icon name="close" /></button>
          <template v-if="!orderPlaced">
            <div class="checkout-form-panel"><p class="eyebrow">Secure checkout</p><h2>Delivery details</h2><form id="checkout-form" @submit.prevent="placeOrder"><div class="field-row"><label>First name<input v-model="checkoutForm.firstName" required type="text" /></label><label>Last name<input v-model="checkoutForm.lastName" required type="text" /></label></div><label>Email address<input v-model="checkoutForm.email" required type="email" /></label><label>Country / region<input v-model="checkoutForm.country" required type="text" /></label><label>Address<input v-model="checkoutForm.address" required type="text" /></label><div class="field-row"><label>City<input v-model="checkoutForm.city" required type="text" /></label><label>Postal code<input v-model="checkoutForm.postal" required type="text" /></label></div></form></div>
            <aside class="checkout-summary"><p class="eyebrow">Your order</p><div class="checkout-mini-items"><div v-for="item in cartItems" :key="item.id"><img :src="asset(item.image)" :alt="item.name" /><span><strong>{{ item.name }}</strong><small>Qty {{ item.quantity }}</small></span><b>{{ money(item.price * item.quantity) }}</b></div></div><div class="checkout-total"><p><span>Delivery</span><span>{{ cartSubtotal >= 250 ? 'Complimentary' : '$18' }}</span></p><p><strong>Total</strong><strong>{{ money(cartSubtotal + (cartSubtotal >= 250 ? 0 : 18)) }}</strong></p></div><button class="button button--gold" type="submit" form="checkout-form">Complete demo order</button><small class="demo-note"><Icon name="shield" :size="14" /> Front-end demonstration—no payment is processed.</small></aside>
          </template>
          <div v-else class="order-success"><span class="success-pearl"><Icon name="check" :size="30" /></span><p class="eyebrow">Order received</p><h2>Thank you, {{ checkoutForm.firstName }}.</h2><p>Your demonstration order <strong>{{ orderNumber }}</strong> is complete. No payment was processed.</p><button class="button button--gold" type="button" @click="closeCheckout">Return to Lunara</button></div>
        </section>
      </div>
    </Transition>

    <Transition name="toast">
      <div v-if="toastMessage" class="toast-message" role="status"><Icon name="check" :size="16" /><span>{{ toastMessage }}</span></div>
    </Transition>
  </div>
</template>
