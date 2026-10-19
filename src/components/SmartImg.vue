<script setup>
import { computed } from 'vue'

// Busca en src/assets/img cualquier archivo cuyo nombre base coincida,
// sin importar la extensión (webp, jpg, jpeg, png, avif, svg, gif).
const files = import.meta.glob('../assets/img/*.{webp,jpg,jpeg,png,avif,svg,gif,WEBP,JPG,JPEG,PNG}', {
  eager: true, query: '?url', import: 'default'
})
const index = {}
for (const [path, url] of Object.entries(files)) {
  const base = path.split('/').pop().replace(/\.[^.]+$/, '').toLowerCase()
  index[base] = url
}

const props = defineProps({
  name: { type: String, required: true },
  alt: { type: String, default: '' },
  eager: Boolean
})
const src = computed(() => index[props.name.toLowerCase()] || null)
</script>

<template>
  <img v-if="src" :src="src" :alt="alt" :loading="eager ? 'eager' : 'lazy'" decoding="async">
  <div v-else class="img-missing" role="img" :aria-label="alt">Falta imagen: {{ name }}</div>
</template>
