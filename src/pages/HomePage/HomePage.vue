<script setup>
import { journalArticles, products } from '../../data/catalog.js'
import HeroSection from '../../components/home/HeroSection/HeroSection.vue'
import AssuranceSection from '../../components/home/AssuranceSection/AssuranceSection.vue'
import BrandMarqueeSection from '../../components/home/BrandMarqueeSection/BrandMarqueeSection.vue'
import CategoryCollectionSection from '../../components/home/CategoryCollectionSection/CategoryCollectionSection.vue'
import JewelleryShowcaseSection from '../../components/home/JewelleryShowcaseSection/JewelleryShowcaseSection.vue'
import EditorialCampaignSection from '../../components/home/EditorialCampaignSection/EditorialCampaignSection.vue'
import SignatureWorksSection from '../../components/home/SignatureWorksSection/SignatureWorksSection.vue'
import PearlOrbitSection from '../../components/home/PearlOrbitSection/PearlOrbitSection.vue'
import PearlsReimaginedSection from '../../components/home/PearlsReimaginedSection/PearlsReimaginedSection.vue'
import CraftsmanshipProcessSection from '../../components/home/CraftsmanshipProcessSection/CraftsmanshipProcessSection.vue'
import LifestyleGallerySection from '../../components/home/LifestyleGallerySection/LifestyleGallerySection.vue'
import MuseStorySection from '../../components/home/MuseStorySection/MuseStorySection.vue'
import OrganicPearlStorySection from '../../components/home/OrganicPearlStorySection/OrganicPearlStorySection.vue'
import BrandFilmSection from '../../components/home/BrandFilmSection/BrandFilmSection.vue'
import FeaturedProductsSection from '../../components/home/FeaturedProductsSection/FeaturedProductsSection.vue'
import PearlFinderSection from '../../components/home/PearlFinderSection/PearlFinderSection.vue'
import AtelierStorySection from '../../components/home/AtelierStorySection/AtelierStorySection.vue'
import GiftingSection from '../../components/home/GiftingSection/GiftingSection.vue'
import CraftRitualSection from '../../components/home/CraftRitualSection/CraftRitualSection.vue'
import JournalPreviewSection from '../../components/home/JournalPreviewSection/JournalPreviewSection.vue'
import PearlDiarySection from '../../components/home/PearlDiarySection/PearlDiarySection.vue'
import ConciergeSection from '../../components/home/ConciergeSection/ConciergeSection.vue'
import NewsletterSection from '../../components/home/NewsletterSection/NewsletterSection.vue'

defineProps({
  wishlist: { type: Array, default: () => [] },
})

defineEmits([
  'open-shop',
  'navigate',
  'add-to-cart',
  'toggle-wish',
  'open-quick',
  'open-product',
])
</script>

<template>
  <HeroSection @explore="$emit('open-shop', $event)" @navigate="$emit('navigate', $event)" />
  <AssuranceSection />
  <BrandMarqueeSection />
  <CategoryCollectionSection @explore="$emit('open-shop', $event)" />
  <JewelleryShowcaseSection @explore="$emit('open-shop', $event)" />
  <MuseStorySection @explore="$emit('open-shop', $event)" />
  <PearlOrbitSection @explore="$emit('open-shop', $event)" />
  <FeaturedProductsSection
    :products="products.slice(0, 4)"
    :wishlist="wishlist"
    @explore="$emit('open-shop', $event)"
    @add="$emit('add-to-cart', $event)"
    @wish="$emit('toggle-wish', $event)"
    @open="$emit('open-quick', $event)"
  />
  <BrandFilmSection />
  <SignatureWorksSection @explore="$emit('open-shop', $event)" /> 
  <GiftingSection @explore="$emit('open-shop', $event)" />
  <CraftRitualSection />
  <EditorialCampaignSection @explore="$emit('open-shop', $event)" />
  <JournalPreviewSection :articles="journalArticles" @navigate="$emit('navigate', $event)" />
  <PearlDiarySection />
  <ConciergeSection @navigate="$emit('navigate', $event)" />



<PearlsReimaginedSection @explore="$emit('open-shop', $event)" /> 
  <PearlFinderSection @open-product="$emit('open-product', $event)" /> 
  <CraftsmanshipProcessSection @explore="$emit('open-shop', $event)" />
  <LifestyleGallerySection @explore="$emit('open-shop', $event)" /> 
  <AtelierStorySection @navigate="$emit('navigate', $event)" />
  <OrganicPearlStorySection @navigate="$emit('navigate', $event)" /> 

</template>
