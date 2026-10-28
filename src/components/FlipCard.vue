<script setup>
import { ref, watch, onBeforeUnmount } from 'vue'

// Tarjeta tipo "flip clock": la mitad de arriba cae y muestra el número nuevo.
const props = defineProps({ value: { type: String, required: true } })

const shown = ref(props.value)   // número nuevo
const old = ref(props.value)     // número anterior (mientras cae la tarjeta)
const flipping = ref(false)
const tick = ref(0)
let tm

const reduce = typeof matchMedia !== 'undefined' && matchMedia('(prefers-reduced-motion: reduce)').matches

watch(() => props.value, n => {
  if (n === shown.value) return
  clearTimeout(tm)
  old.value = shown.value
  shown.value = n
  if (reduce) { old.value = n; return }
  flipping.value = true
  tick.value++
  tm = setTimeout(() => { flipping.value = false; old.value = shown.value }, 700)
})

onBeforeUnmount(() => clearTimeout(tm))
</script>

<template>
  <div class="cd-card">
    <div class="fh t"><i>{{ shown }}</i></div>
    <div class="fh b"><i>{{ flipping ? old : shown }}</i></div>
    <div v-if="flipping" :key="'t' + tick" class="fh t flap-t"><i>{{ old }}</i></div>
    <div v-if="flipping" :key="'b' + tick" class="fh b flap-b"><i>{{ shown }}</i></div>
    <span class="cd-line"></span>
    <i class="cd-pin cd-pin-l"></i><i class="cd-pin cd-pin-r"></i>
  </div>
</template>
