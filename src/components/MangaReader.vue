<template>
<div class="book-shell reader-appear" :style="bookShellStyle">
  <div class="hint-bar">◀ PREV PAGE — CLICK COVER TO OPEN — NEXT PAGE ▶</div>

  <div class="scene-wrapper">
  <div class="scene" :style="{ transform: sceneTransform }">
    <div class="book" :style="{ transform: bookTransform }">

      <!-- Back cover -->
      <div class="back-cover">
        <div class="bc-label">JUMP COMICS</div>
        <div class="bc-barcode">
          <div style="width:1px;height:34px;background:#111;"></div>
          <div style="width:2px;height:34px;background:#222;"></div>
          <div style="width:1px;height:34px;background:#2a2a2a;"></div>
          <div style="width:3px;height:34px;background:#222;"></div>
          <div style="width:1px;height:34px;background:#333;"></div>
          <div style="width:2px;height:34px;background:#2a2a2a;"></div>
          <div style="width:1px;height:34px;background:#333;"></div>
          <div style="width:2px;height:34px;background:#222;"></div>
          <div style="width:3px;height:34px;background:#2a2a2a;"></div>
        </div>
        <div class="bc-isbn">ISBN 978-0-DEV-JORGE-1</div>
      </div>

      <!-- Spine -->
      <div class="spine">
        <span class="spine-title">JORGE AGUILAR</span>
        <div class="spine-bar"></div>
        <span class="spine-sub">FULL STACK DEV</span>
        <div class="spine-bar"></div>
        <span class="spine-vol">VOL.1</span>
      </div>

      <!-- Page edges -->
      <div class="page-edges">
        <div v-for="n in TOTAL_PAGES * 3" :key="n" class="page-edge-strip"
          :style="{ display: remainingEdges(n - 1) ? 'block' : 'none' }"></div>
      </div>

      <!-- Pages -->
      <div v-for="i in TOTAL_PAGES" :key="i - 1" class="page"
        :class="{ flipped: isFlipped(i - 1) }"
        :style="{ zIndex: pageZIndex(i - 1) }"
        @click="handlePageClick(i - 1)">

        <div class="page-front">
          <Cover            v-if="i - 1 === 0" />
          <HeroIntro        v-else-if="i - 1 === 1" />
          <BattleLogPart2   v-else-if="i - 1 === 2" />
          <SkillTreePart2   v-else-if="i - 1 === 3" />
          <TrainingArcPart2 v-else-if="i - 1 === 4" />
        </div>

        <div class="page-back">
          <TableOfContents  v-if="i - 1 === 0" />
          <BattleLogPart1   v-else-if="i - 1 === 1" />
          <SkillTreePart1   v-else-if="i - 1 === 2" />
          <TrainingArcPart1 v-else-if="i - 1 === 3" />
          <Contact          v-else-if="i - 1 === 4" />
        </div>

      </div>
    </div>
  </div>
  </div>

  <div class="nav-arrows">
    <button class="arrow-btn" :disabled="isPrevDisabled" @click="prevPage">◀ PREV</button>
    <span class="page-indicator">{{ indicatorText }}</span>
    <button class="arrow-btn" :disabled="isNextDisabled" @click="nextPage">NEXT ▶</button>
  </div>
  <button class="back-shelf-btn" @click="emit('back')">← BACK TO SHELF</button>
</div>
</template>

<style scoped>
.book-shell { display: flex; flex-direction: column; align-items: center; }
.scene-wrapper { position: relative; display: flex; align-items: center; justify-content: center; }
.hint-bar {
  font-family: 'Bangers', cursive;
  font-size: 0.6rem; letter-spacing: 3px;
  color: rgba(255,255,255,0.2);
  margin-bottom: 0.8rem; text-align: center;
}
.scene {
  perspective: 1600px;
  display: flex; align-items: center; justify-content: center;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}
.book {
  position: relative; width: 300px; height: 520px;
  transform-style: preserve-3d;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}
.back-cover {
  position: absolute; left: 0; top: 0; width: 300px; height: 520px;
  background: #fff; border: 2px solid #1a1a1a; overflow: hidden;
  display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 0.8rem;
  transform: translateZ(-2px);
}
.back-cover::before {
  content: ''; position: absolute; inset: 0;
  background-image: radial-gradient(circle, #333 0.5px, transparent 0.5px);
  background-size: 5px 5px; opacity: 0.25;
}
.bc-label { font-family: 'Bangers', cursive; font-size: 0.62rem; letter-spacing: 4px; color: #888; position: relative; z-index: 1; }
.bc-barcode { display: flex; gap: 1.5px; position: relative; z-index: 1; margin-top: 0.8rem; }
.bc-isbn { font-family: monospace; font-size: 0.42rem; color: #888; position: relative; z-index: 1; margin-top: 0.5rem; }
.spine {
  position: absolute; left: -18px; top: 0; width: 18px; height: 520px;
  background: linear-gradient(to right, #080808, #1c1c1c, #080808);
  transform-origin: right center; transform: rotateY(-90deg);
  display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 0.4rem;
  border: 1px solid #2a2a2a; overflow: hidden;
}
.spine-title { font-family: 'Bangers', cursive; font-size: 0.65rem; letter-spacing: 2px; color: #c8942a; writing-mode: vertical-rl; transform: rotate(180deg); }
.spine-bar { width: 1px; height: 28px; background: #c8942a; opacity: 0.4; }
.spine-sub { font-family: 'Bangers', cursive; font-size: 0.48rem; letter-spacing: 1.5px; color: #555; writing-mode: vertical-rl; transform: rotate(180deg); }
.spine-vol { font-family: 'Bangers', cursive; font-size: 0.55rem; letter-spacing: 1px; color: #444; writing-mode: vertical-rl; transform: rotate(180deg); }
.page-edges { position: absolute; right: -8px; top: 3px; width: 8px; height: 514px; display: flex; flex-direction: column; overflow: hidden; }
.page-edge-strip { flex: 1; background: linear-gradient(to right, #e8e3db, #f0ebe2); border-top: 0.5px solid #d5d0c8; }
.page {
  position: absolute; left: 0; top: 0; width: 300px; height: 520px;
  transform-origin: left center; transform-style: preserve-3d;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
  cursor: pointer;
}
.page-front, .page-back {
  position: absolute; inset: 0; backface-visibility: hidden;
  overflow: hidden; border: 2.5px solid #0a0a0a; display: flex; flex-direction: column;
}
.page-front { border-left: none; }
.page-back  { border-left: none; transform: rotateY(180deg); }
.page.flipped { transform: rotateY(-180deg); }
.nav-arrows { display: flex; align-items: center; gap: 1.5rem; margin-top: 1rem; position: relative; z-index: 10; }
.arrow-btn {
  font-family: 'Bangers', cursive; font-size: 0.8rem; letter-spacing: 2px;
  background: none; border: 1.5px solid rgba(255,255,255,0.2);
  color: rgba(255,255,255,0.5); padding: 0.38rem 1.1rem;
  cursor: pointer; transition: all 0.18s;
}
.arrow-btn:hover { border-color: #c8942a; color: #c8942a; }
.arrow-btn:disabled { opacity: 0.2; cursor: default; }
.page-indicator { font-family: 'Bangers', cursive; font-size: 0.65rem; letter-spacing: 3px; color: rgba(255,255,255,0.3); }
.back-shelf-btn {
  font-family: 'Bangers', cursive; font-size: 0.6rem; letter-spacing: 2px;
  background: none; border: none; color: rgba(255,255,255,0.2);
  cursor: pointer; margin-top: 0.3rem; transition: color 0.18s;
}
.back-shelf-btn:hover { color: #c8942a; }
.reader-appear { animation: reader-fade-in 0.55s ease forwards; }
@keyframes reader-fade-in { from { opacity: 0; } to { opacity: 1; } }
</style>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import type { CSSProperties } from 'vue'
import Cover from './reader/pages/Cover.vue'
import TableOfContents from './reader/pages/TableOfContents.vue'
import HeroIntro from './reader/pages/HeroIntro.vue'
import BattleLogPart1 from './reader/pages/BattleLogPart1.vue'
import BattleLogPart2 from './reader/pages/BattleLogPart2.vue'
import SkillTreePart1 from './reader/pages/SkillTreePart1.vue'
import SkillTreePart2 from './reader/pages/SkillTreePart2.vue'
import TrainingArcPart1 from './reader/pages/TrainingArcPart1.vue'
import TrainingArcPart2 from './reader/pages/TrainingArcPart2.vue'
import Contact from './reader/pages/Contact.vue'

const props = defineProps<{
  bookShellStyle: CSSProperties
  bookScale: number
}>()
const emit = defineEmits<{ back: [] }>()

const TOTAL_PAGES = 5
const currentPage = ref(0)

const bookIsOpen = computed(() => currentPage.value > 0)
const isMobile = computed(() => props.bookScale <= 1)
const mobilePage = ref(0)
const isTransitioning = ref(false)

const sceneTransform = computed(() => {
  if (!bookIsOpen.value) return 'translateX(0) translateZ(0)'
  if (props.bookScale > 1) return 'translateX(150px) translateZ(0)'
  return mobilePage.value === 0 ? 'translateX(300px) translateZ(0)' : 'translateX(0) translateZ(0)'
})
const bookTransform = computed(() =>
  bookIsOpen.value ? 'rotateX(3deg) translateZ(0)' : 'rotateY(-20deg) rotateX(5deg) translateZ(0)'
)

const pageLabels = ['COVER', 'PAGE 1', 'PAGE 2', 'PAGE 3', 'PAGE 4', 'END']
const indicatorText = computed(() => `${pageLabels[currentPage.value]} / ${TOTAL_PAGES}`)
const isPrevDisabled = computed(() => currentPage.value === 0)
const isNextDisabled = computed(() => {
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 0) return false
  return currentPage.value === TOTAL_PAGES
})

function pageZIndex(i: number) { return i < currentPage.value ? i : TOTAL_PAGES - i }
function isFlipped(i: number) { return i < currentPage.value }
function remainingEdges(i: number) { return i < (TOTAL_PAGES - currentPage.value) * 3 }

function nextPage() {
  if (isTransitioning.value) return
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 0) { mobilePage.value = 1; return }
  if (currentPage.value >= TOTAL_PAGES) return
  // On mobile: pre-set mobilePage=1 so opening the book keeps the scene still
  if (isMobile.value && !bookIsOpen.value) mobilePage.value = 1
  currentPage.value++
  if (isMobile.value) {
    // Page flips first (scene stays put), then scene slides to show left side
    isTransitioning.value = true
    setTimeout(() => { mobilePage.value = 0; isTransitioning.value = false }, 800)
  } else {
    mobilePage.value = 0
  }
}
function prevPage() {
  if (isTransitioning.value) return
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 1) { mobilePage.value = 0; return }
  if (currentPage.value <= 0) return
  if (isMobile.value) {
    // Scene slides to right side first, then page unflips
    isTransitioning.value = true
    mobilePage.value = 1
    setTimeout(() => { currentPage.value--; isTransitioning.value = false }, 800)
  } else {
    currentPage.value--
  }
}
function handlePageClick(i: number) {
  if (isFlipped(i)) prevPage()
  else nextPage()
}

let touchStartX = 0
function onKeydown(e: KeyboardEvent) {
  if (e.key === 'ArrowRight') nextPage()
  if (e.key === 'ArrowLeft') prevPage()
}
function onTouchStart(e: TouchEvent) { touchStartX = e.touches[0].clientX }
function onTouchEnd(e: TouchEvent) {
  const d = touchStartX - e.changedTouches[0].clientX
  if (d > 50) nextPage()
  if (d < -50) prevPage()
}

onMounted(() => {
  document.addEventListener('keydown', onKeydown)
  document.addEventListener('touchstart', onTouchStart)
  document.addEventListener('touchend', onTouchEnd)
})
onUnmounted(() => {
  document.removeEventListener('keydown', onKeydown)
  document.removeEventListener('touchstart', onTouchStart)
  document.removeEventListener('touchend', onTouchEnd)
})
</script>
