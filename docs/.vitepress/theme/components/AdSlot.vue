<template>
  <div v-if="isEnabled" class="ad-container">

    <ins ref="adRef" class="adsbygoogle"
         style="display:block"
         data-ad-client="ca-pub-1238777311285568"
         data-ad-slot="8138079461"
         data-ad-format="auto"
         data-full-width-responsive="true"></ins>
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
      slot: '8138079461',
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
.ad-container {
  margin: 2rem 0;
  padding: 1rem 0;
  border-top: 1px solid var(--vp-c-divider);
}


</style>
