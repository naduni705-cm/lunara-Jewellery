# Lunara Pearl Jewellery — Vue 3 Frontend

This version reorganizes the frontend so the homepage is easy to understand and maintain.

## Run

```bash
npm install
npm run dev
```

Production build:

```bash
npm run build
```

## Main frontend structure

```text
src/
├── App.vue
├── main.js
├── assets/
│   └── styles/
│       └── main.css
├── data/
│   └── catalog.js
├── pages/
│   └── HomePage/
│       └── HomePage.vue
└── components/
    ├── shared/
    │   ├── Icon/
    │   │   └── Icon.vue
    │   └── ProductCard/
    │       └── ProductCard.vue
    └── home/
        ├── HeroSection/
        │   └── HeroSection.vue
        ├── AssuranceSection/
        │   └── AssuranceSection.vue
        ├── BrandMarqueeSection/
        │   └── BrandMarqueeSection.vue
        ├── CategoryCollectionSection/
        │   └── CategoryCollectionSection.vue
        ├── JewelleryShowcaseSection/
        │   └── JewelleryShowcaseSection.vue
        ├── EditorialCampaignSection/
        │   └── EditorialCampaignSection.vue
        ├── SignatureWorksSection/
        │   └── SignatureWorksSection.vue
        ├── PearlOrbitSection/
        │   └── PearlOrbitSection.vue
        ├── PearlsReimaginedSection/
        │   └── PearlsReimaginedSection.vue
        ├── CraftsmanshipProcessSection/
        │   └── CraftsmanshipProcessSection.vue
        ├── LifestyleGallerySection/
        │   └── LifestyleGallerySection.vue
        ├── MuseStorySection/
        │   └── MuseStorySection.vue
        ├── OrganicPearlStorySection/
        │   └── OrganicPearlStorySection.vue
        ├── BrandFilmSection/
        │   └── BrandFilmSection.vue
        ├── FeaturedProductsSection/
        │   └── FeaturedProductsSection.vue
        ├── PearlFinderSection/
        │   └── PearlFinderSection.vue
        ├── AtelierStorySection/
        │   └── AtelierStorySection.vue
        ├── GiftingSection/
        │   └── GiftingSection.vue
        ├── CraftRitualSection/
        │   └── CraftRitualSection.vue
        ├── JournalPreviewSection/
        │   └── JournalPreviewSection.vue
        ├── PearlDiarySection/
        │   └── PearlDiarySection.vue
        ├── ConciergeSection/
        │   └── ConciergeSection.vue
        └── NewsletterSection/
            └── NewsletterSection.vue
```

## What each main folder does

- `pages/HomePage/HomePage.vue` only assembles homepage sections in the correct order.
- `components/home/` contains complete homepage sections.
- `components/shared/` contains small reusable components used by multiple pages/sections.
- `data/catalog.js` contains product, collection and journal data.
- `assets/styles/main.css` contains the existing global visual system and responsive styles.
- `public/images/` and `public/videos/` contain media assets.
- `App.vue` keeps the application-level navigation, shop/cart/wishlist, overlays and non-home views.

## Validation

All local Vue/JS imports, referenced public media paths and JavaScript syntax in Vue script blocks were checked after the refactor.

The execution environment used to prepare this ZIP could not complete `npm install` because the npm registry dependency was unavailable in the local cache, so run `npm install` on your development machine before `npm run dev` or `npm run build`.
