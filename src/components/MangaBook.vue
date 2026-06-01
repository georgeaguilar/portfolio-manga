<template>
  <BookShelf v-if="appScene === 'shelf'" :case-style="caseStyle" @confirm="onConfirm" />
  <MangaReader v-else :book-shell-style="bookShellStyle" :book-scale="bookScale" @back="onBack" />
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import type { CSSProperties } from 'vue'
import BookShelf from './BookShelf.vue'
import MangaReader from './MangaReader.vue'

const appScene = ref<'shelf' | 'reader'>('shelf')

const bookScale = ref(1)
const NATURAL_H = 600
const NATURAL_CASE_W = 458
const NATURAL_CASE_H = 750
const caseScale = ref(1)

const bookShellStyle = computed<CSSProperties>(() => {
  const s = bookScale.value
  if (Math.abs(s - 1) < 0.01) return {}
  return {
    transform: `scale(${s})`,
    transformOrigin: 'top center',
    marginBottom: `${Math.round(NATURAL_H * (s - 1))}px`,
  }
})

const caseStyle = computed<CSSProperties>(() => {
  const s = caseScale.value
  if (Math.abs(s - 1) < 0.01) return {}
  return {
    transform: `scale(${s})`,
    transformOrigin: 'top center',
    marginBottom: `${Math.round(NATURAL_CASE_H * (s - 1))}px`,
  }
})

function updateScale() {
  const vw = window.innerWidth
  const vh = window.innerHeight
  let byW: number
  if (vw < 382) {
    byW = Math.max(0.6, (vw - 32) / 350)
  } else if (vw >= 620) {
    byW = Math.min(1.6, (vw - 32) / 480)
  } else {
    byW = 1
  }
  const byH = Math.min(1.6, (vh - 32) / NATURAL_H)
  bookScale.value = parseFloat(Math.min(byW, byH).toFixed(3))

  const csByW = Math.min(1, (vw - 24) / NATURAL_CASE_W)
  const csByH = Math.min(1, (vh - 160) / NATURAL_CASE_H)
  caseScale.value = parseFloat(Math.max(0.45, Math.min(csByW, csByH)).toFixed(3))
}

function onConfirm() { appScene.value = 'reader' }
function onBack() { appScene.value = 'shelf' }

onMounted(() => {
  updateScale()
  window.addEventListener('resize', updateScale)
})
onUnmounted(() => {
  window.removeEventListener('resize', updateScale)
})
</script>
