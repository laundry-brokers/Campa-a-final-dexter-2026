<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import FlipCard from './FlipCard.vue'

// Fecha en la que termina la promoción (cámbiala aquí cuando necesites otra).
// Formato: AAAA-MM-DDTHH:MM:SS-06:00  (hora centro de México)
const props = defineProps({
  end: { type: String, default: '2026-11-30T23:59:59-06:00' }
})

const target = new Date(props.end).getTime()
const now = ref(Date.now())
let t

const left = computed(() => Math.max(0, Math.floor((target - now.value) / 1000)))
const pad = n => String(n).padStart(2, '0')

const units = computed(() => {
  const s = left.value
  return [
    { k: 'dias', label: 'Días', v: pad(Math.floor(s / 86400)) },
    { k: 'horas', label: 'Horas', v: pad(Math.floor((s % 86400) / 3600)) },
    { k: 'min', label: 'Minutos', v: pad(Math.floor((s % 3600) / 60)) },
    { k: 'seg', label: 'Segundos', v: pad(s % 60) }
  ]
})

onMounted(() => { t = setInterval(() => { now.value = Date.now() }, 1000) })
onBeforeUnmount(() => clearInterval(t))
</script>

<template>
  <div class="cd" role="timer" aria-label="Tiempo restante de la promoción">
    <p class="cd-title">Tu promoción termina en:</p>
    <div class="cd-row">
      <div v-for="u in units" :key="u.k" class="cd-unit" :class="{ 'is-days': u.k === 'dias' }">
        <FlipCard :value="u.v" />
        <span class="cd-label">{{ u.label }}</span>
      </div>
    </div>
  </div>
</template>
