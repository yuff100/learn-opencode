<template>
  <div v-if="isEnabled" class="ad-in-article">
    <ins ref="adRef" class="adsbygoogle"
         style="display:block; text-align:center;"
         data-ad-layout="in-article"
         data-ad-format="fluid"
         data-ad-client="ca-pub-1238777311285568"
         data-ad-slot="9628105168"></ins>
  </div>
</template>

<script setup>
import { onMounted, ref, nextTick } from 'vue'

const isEnabled = ref(false)
const adRef = ref(null)
const allowedHosts = new Set(['learnopencode.com', 'www.learnopencode.com'])

const isAllowedHost = () => {
  if (typeof window === 'undefined') {
    return false
  }

  return allowedHosts.has(window.location.hostname)
}

const initAd = () => {
  const adElement = adRef.value

  if (!adElement) {
    return
  }

  if (adElement.getAttribute('data-adsbygoogle-status') || adElement.getAttribute('data-ad-status')) {
    return
  }

  try {
    (window.adsbygoogle = window.adsbygoogle || []).push({})
  } catch (error) {
    isEnabled.value = false
    console.error('AdSense init failed', {
      slot: '9628105168',
      host: window.location.hostname,
      error
    })
  }
}

onMounted(async () => {
  if (!isAllowedHost()) {
    return
  }

  isEnabled.value = true
  await nextTick()
  initAd()
})
</script>

<style scoped>
.ad-in-article {
  margin: 2rem 0;
}
</style>
