<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import coverImg from '../assets/cover.png'
import originArcImg from '../assets/origin-arc.png'

const TOTAL_PAGES = 5
const currentPage = ref(0)

// Open/close animation
const bookIsOpen = computed(() => currentPage.value > 0)
const isMobile = computed(() => bookScale.value <= 1)
// 0 = left page (back of prev), 1 = right page (front of current)
const mobilePage = ref(0)

// Scene management
const appScene = ref<'shelf' | 'reader'>('shelf')
const showConfirm = ref(false)
const isLeaving = ref(false)

const caseScale = ref(1)
const NATURAL_CASE_W = 458 // bookcase total width incl. borders
const NATURAL_CASE_H = 750 // bookcase total height incl. borders

const caseStyle = computed(() => {
  const s = caseScale.value
  if (Math.abs(s - 1) < 0.01) return {}
  return {
    transform: `scale(${s})`,
    transformOrigin: 'top center',
    marginBottom: `${Math.round(NATURAL_CASE_H * (s - 1))}px`,
  }
})

const isSpineHovered = ref(false)
function onSpineEnter() { isSpineHovered.value = true }
function onSpineLeave() { isSpineHovered.value = false }

function goToShelf() {
  appScene.value = 'shelf'
  currentPage.value = 0
  mobilePage.value = 0
  isLeaving.value = false
}

function onBookClick() { showConfirm.value = true }
function onConfirmYes() {
  showConfirm.value = false
  isLeaving.value = true
  setTimeout(() => { appScene.value = 'reader' }, 900)
}
function onConfirmNo() { showConfirm.value = false }

const sceneTransform = computed(() => {
  if (!bookIsOpen.value) return 'translateX(0)'
  if (bookScale.value > 1) return 'translateX(150px)' // tablet: center spread
  // Mobile: translateX(300px) shows left page centered, translateX(0) shows right page
  return mobilePage.value === 0 ? 'translateX(300px)' : 'translateX(0)'
})
const bookTransform = computed(() =>
  bookIsOpen.value ? 'rotateX(3deg)' : 'rotateY(-20deg) rotateX(5deg)'
)

// Responsive scaling
const bookScale = ref(1)
const NATURAL_H = 600 // approximate natural height of book-shell content

function updateScale() {
  const vw = window.innerWidth
  const vh = window.innerHeight
  // Open spread = ~620px (two 300px pages + spine); single page cover = ~350px
  let byW: number
  if (vw < 382) {
    byW = Math.max(0.6, (vw - 32) / 350) // small phones: scale down
  } else if (vw >= 620) {
    byW = Math.min(1.6, (vw - 32) / 480) // tablets: scale up based on single-page width
  } else {
    byW = 1 // normal phones: natural size
  }
  const byH = Math.min(1.6, (vh - 32) / NATURAL_H)
  bookScale.value = parseFloat(Math.min(byW, byH).toFixed(3))

  const csByW = Math.min(1, (vw - 24) / NATURAL_CASE_W)
  const csByH = Math.min(1, (vh - 160) / NATURAL_CASE_H)
  caseScale.value = parseFloat(Math.max(0.45, Math.min(csByW, csByH)).toFixed(3))
}

const bookShellStyle = computed(() => {
  const s = bookScale.value
  if (Math.abs(s - 1) < 0.01) return {}
  return {
    transform: `scale(${s})`,
    transformOrigin: 'top center',
    marginBottom: `${Math.round(NATURAL_H * (s - 1))}px`,
  }
})

const pageLabels = ['COVER', 'PAGE 1', 'PAGE 2', 'PAGE 3', 'PAGE 4', 'END']
const indicatorText = computed(() => `${pageLabels[currentPage.value]} / ${TOTAL_PAGES}`)
const isPrevDisabled = computed(() => currentPage.value === 0)
const isNextDisabled = computed(() => {
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 0) return false
  return currentPage.value === TOTAL_PAGES
})

function pageZIndex(i: number) {
  return i < currentPage.value ? i : TOTAL_PAGES - i
}
function isFlipped(i: number) {
  return i < currentPage.value
}
function remainingEdges(i: number) {
  return i < (TOTAL_PAGES - currentPage.value) * 3
}

function nextPage() {
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 0) {
    mobilePage.value = 1
    return
  }
  if (currentPage.value >= TOTAL_PAGES) return
  currentPage.value++
  mobilePage.value = 0
}
function prevPage() {
  if (isMobile.value && bookIsOpen.value && mobilePage.value === 1) {
    mobilePage.value = 0
    return
  }
  if (currentPage.value <= 0) return
  currentPage.value--
  mobilePage.value = isMobile.value ? 1 : 0
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
  updateScale()
  window.addEventListener('resize', updateScale)
})
onUnmounted(() => {
  document.removeEventListener('keydown', onKeydown)
  document.removeEventListener('touchstart', onTouchStart)
  document.removeEventListener('touchend', onTouchEnd)
  window.removeEventListener('resize', updateScale)
})
</script>

<template>

<!-- SHELF SCENE -->
<div v-if="appScene === 'shelf'" class="shelf-scene" :class="{ 'shelf-leaving': isLeaving }">

  <div class="shelf-header">
    <div style="font-family:'Bangers',cursive;font-size:0.5rem;letter-spacing:5px;color:rgba(255,255,255,0.25);margin-bottom:0.6rem;">JUMP COMICS PRESENTS</div>
    <div style="font-family:'Bangers',cursive;font-size:2.8rem;letter-spacing:6px;color:#fff;line-height:0.9;text-shadow:3px 3px 0 #c8942a,-1px -1px 0 #333;">JORGE<br>AGUILAR</div>
    <div style="font-family:'Bangers',cursive;font-size:0.62rem;letter-spacing:5px;color:#c8942a;margin-top:0.4rem;">— FULL STACK DEVELOPER — VOL. 01 —</div>
  </div>

  <div class="bookshelf-wrap" :style="caseStyle">
    <div class="bookcase">
      <!-- Shelf 1 -->
      <div class="shelf-section">
        <div class="books-area">
          <div class="s-book" style="width:16px;height:82px;background:linear-gradient(90deg,#c0392b,#922b21);"></div>
          <div class="s-book" style="width:21px;height:90px;background:linear-gradient(90deg,#2980b9,#1a5276);"></div>
          <div class="s-book" style="width:14px;height:78px;background:linear-gradient(90deg,#27ae60,#1e8449);transform:rotate(-2.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:19px;height:86px;background:linear-gradient(90deg,#8e44ad,#6c3483);transform:rotate(-1deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:24px;height:93px;background:linear-gradient(90deg,#d35400,#a04000);"></div>
          <div class="s-book" style="width:15px;height:80px;background:linear-gradient(90deg,#16a085,#0e6655);"></div>
          <div class="s-book" style="width:20px;height:88px;background:linear-gradient(90deg,#7f8c8d,#626567);transform:rotate(3deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:17px;height:84px;background:linear-gradient(90deg,#f39c12,#d68910);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:13px;height:76px;background:linear-gradient(90deg,#1abc9c,#148f77);"></div>
          <div class="s-book" style="width:22px;height:91px;background:linear-gradient(90deg,#e74c3c,#c0392b);transform:rotate(-4deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:16px;height:81px;background:linear-gradient(90deg,#3498db,#2980b9);transform:rotate(-2deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:18px;height:87px;background:linear-gradient(90deg,#e67e22,#d35400);"></div>
          <div class="s-book" style="width:12px;height:75px;background:linear-gradient(90deg,#9b59b6,#7d3c98);transform:rotate(2.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:20px;height:89px;background:linear-gradient(90deg,#2ecc71,#27ae60);"></div>
          <div class="s-book" style="width:15px;height:83px;background:linear-gradient(90deg,#e74c3c,#c0392b);transform:rotate(1deg);transform-origin:bottom left;"></div>
        </div>
      </div>
      <div class="shelf-divider"></div>
      <!-- Shelf 2 -->
      <div class="shelf-section">
        <div class="books-area">
          <div class="s-book" style="width:18px;height:86px;background:linear-gradient(90deg,#2c3e50,#1a252f);"></div>
          <div class="s-book" style="width:14px;height:79px;background:linear-gradient(90deg,#c0392b,#922b21);transform:rotate(2.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:22px;height:92px;background:linear-gradient(90deg,#27ae60,#1e8449);transform:rotate(1deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:16px;height:83px;background:linear-gradient(90deg,#f39c12,#d68910);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:20px;height:89px;background:linear-gradient(90deg,#8e44ad,#6c3483);"></div>
          <div class="s-book" style="width:13px;height:77px;background:linear-gradient(90deg,#16a085,#0e6655);"></div>
          <div class="s-book" style="width:25px;height:95px;background:linear-gradient(90deg,#d35400,#a04000);"></div>
          <div class="s-book" style="width:17px;height:85px;background:linear-gradient(90deg,#2980b9,#1a5276);transform:rotate(3.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:12px;height:76px;background:linear-gradient(90deg,#1abc9c,#148f77);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:21px;height:91px;background:linear-gradient(90deg,#c0392b,#7b241c);"></div>
          <div class="s-book" style="width:15px;height:81px;background:linear-gradient(90deg,#3498db,#2980b9);transform:rotate(-3deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:19px;height:87px;background:linear-gradient(90deg,#9b59b6,#7d3c98);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:23px;height:93px;background:linear-gradient(90deg,#e67e22,#d35400);"></div>
          <div class="s-book" style="width:14px;height:80px;background:linear-gradient(90deg,#148f77,#117a65);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
        </div>
      </div>
      <div class="shelf-divider"></div>
      <!-- Shelf 3: the manga (middle) -->
      <div class="shelf-section shelf-mid">
        <div class="books-area">
          <div class="s-book" style="width:17px;height:100px;background:linear-gradient(90deg,#1a2a5a,#0f1a40);"></div>
          <div class="s-book" style="width:22px;height:107px;background:linear-gradient(90deg,#3a1a1a,#2a0f0f);transform:rotate(-2deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:15px;height:96px;background:linear-gradient(90deg,#1a3a1a,#0f2a0f);transform:rotate(-1deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:20px;height:103px;background:linear-gradient(90deg,#2a2a1a,#1a1a0f);transform:rotate(2.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:13px;height:94px;background:linear-gradient(90deg,#2c1a3a,#1a0f2a);transform:rotate(1deg);transform-origin:bottom left;"></div>
          <div class="manga-hit" @mouseenter="onSpineEnter" @mouseleave="onSpineLeave" @click="onBookClick">
            <div class="manga-spine" :class="{ 'manga-fly': isLeaving, 'spine-hover': isSpineHovered }">
              <span class="ms-title">JORGE AGUILAR</span>
              <div class="ms-bar"></div>
              <span class="ms-sub">FULL STACK DEV</span>
              <div class="ms-bar"></div>
              <span class="ms-vol">VOL.1</span>
            </div>
          </div>
          <div class="s-book" style="width:18px;height:105px;background:linear-gradient(90deg,#3a1a3a,#2a0f2a);transform:rotate(-3deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:16px;height:98px;background:linear-gradient(90deg,#1a3a3a,#0f2a2a);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:24px;height:109px;background:linear-gradient(90deg,#3a2a1a,#2a1a0f);"></div>
          <div class="s-book" style="width:14px;height:95px;background:linear-gradient(90deg,#1a2a3a,#0f1a2a);"></div>
          <div class="s-book" style="width:21px;height:102px;background:linear-gradient(90deg,#2a1a2a,#1a0f1a);transform:rotate(2deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:16px;height:97px;background:linear-gradient(90deg,#3a2a2a,#2a1a1a);transform:rotate(1deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:19px;height:104px;background:linear-gradient(90deg,#1a3a2a,#0f2a1a);"></div>
        </div>
      </div>
      <div class="shelf-divider"></div>
      <!-- Shelf 4 -->
      <div class="shelf-section">
        <div class="books-area">
          <div class="s-book" style="width:19px;height:84px;background:linear-gradient(90deg,#1e8449,#196f3d);"></div>
          <div class="s-book" style="width:14px;height:78px;background:linear-gradient(90deg,#6c3483,#5b2c6f);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:23px;height:91px;background:linear-gradient(90deg,#a04000,#784212);transform:rotate(-3deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:16px;height:82px;background:linear-gradient(90deg,#0e6655,#0b5345);"></div>
          <div class="s-book" style="width:20px;height:88px;background:linear-gradient(90deg,#d68910,#b7770d);"></div>
          <div class="s-book" style="width:25px;height:94px;background:linear-gradient(90deg,#148f77,#117a65);transform:rotate(2deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:17px;height:85px;background:linear-gradient(90deg,#7f8c8d,#626567);transform:rotate(1deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:13px;height:79px;background:linear-gradient(90deg,#f39c12,#d68910);"></div>
          <div class="s-book" style="width:21px;height:90px;background:linear-gradient(90deg,#2c3e50,#1a252f);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:18px;height:86px;background:linear-gradient(90deg,#c0392b,#922b21);"></div>
          <div class="s-book" style="width:15px;height:81px;background:linear-gradient(90deg,#8e44ad,#6c3483);transform:rotate(3.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:22px;height:92px;background:linear-gradient(90deg,#2980b9,#1a5276);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:12px;height:77px;background:linear-gradient(90deg,#27ae60,#1e8449);"></div>
          <div class="s-book" style="width:19px;height:87px;background:linear-gradient(90deg,#e74c3c,#c0392b);transform:rotate(-2deg);transform-origin:bottom right;"></div>
        </div>
      </div>
      <div class="shelf-divider"></div>
      <!-- Shelf 5 (bottom) -->
      <div class="shelf-section">
        <div class="books-area">
          <div class="s-book" style="width:20px;height:86px;background:linear-gradient(90deg,#922b21,#7b241c);"></div>
          <div class="s-book" style="width:15px;height:79px;background:linear-gradient(90deg,#1a5276,#154360);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:24px;height:92px;background:linear-gradient(90deg,#1e8449,#196f3d);transform:rotate(1deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:16px;height:82px;background:linear-gradient(90deg,#6c3483,#5b2c6f);transform:rotate(2.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:21px;height:89px;background:linear-gradient(90deg,#a04000,#784212);"></div>
          <div class="s-book" style="width:14px;height:77px;background:linear-gradient(90deg,#0e6655,#0b5345);"></div>
          <div class="s-book" style="width:19px;height:85px;background:linear-gradient(90deg,#d68910,#b7770d);transform:rotate(-3.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:25px;height:93px;background:linear-gradient(90deg,#148f77,#117a65);transform:rotate(-1.5deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:17px;height:83px;background:linear-gradient(90deg,#7f8c8d,#626567);transform:rotate(1.5deg);transform-origin:bottom left;"></div>
          <div class="s-book" style="width:13px;height:78px;background:linear-gradient(90deg,#f39c12,#d68910);"></div>
          <div class="s-book" style="width:22px;height:90px;background:linear-gradient(90deg,#2c3e50,#1a252f);"></div>
          <div class="s-book" style="width:18px;height:84px;background:linear-gradient(90deg,#c0392b,#922b21);transform:rotate(-2deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:15px;height:80px;background:linear-gradient(90deg,#8e44ad,#6c3483);transform:rotate(-1deg);transform-origin:bottom right;"></div>
          <div class="s-book" style="width:20px;height:88px;background:linear-gradient(90deg,#3498db,#2980b9);transform:rotate(1deg);transform-origin:bottom left;"></div>
        </div>
      </div>
    </div>
  </div>

  <div class="shelf-hint">◆ CLICK TO READ ◆</div>

  <Transition name="pop">
    <div v-if="showConfirm" class="confirm-overlay" @click.self="onConfirmNo">
      <div class="confirm-box">
        <div style="font-family:'Bangers',cursive;font-size:0.48rem;letter-spacing:4px;color:#c8942a;margin-bottom:0.4rem;">JUMP COMICS</div>
        <div style="font-family:'Bangers',cursive;font-size:1.5rem;letter-spacing:2px;color:#0a0a0a;line-height:1.2;margin-bottom:1rem;">¿QUIERES LEER<br>ESTE MANGA?</div>
        <div style="display:flex;gap:8px;justify-content:center;">
          <button class="confirm-btn-yes" @click="onConfirmYes">¡SÍ, LEER!</button>
          <button class="confirm-btn-no" @click="onConfirmNo">NO</button>
        </div>
      </div>
    </div>
  </Transition>

</div>

<!-- READER SCENE -->
<div v-else class="book-shell reader-appear" :style="bookShellStyle">
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
        <div
          v-for="n in TOTAL_PAGES * 3"
          :key="n"
          class="page-edge-strip"
          :style="{ display: remainingEdges(n - 1) ? 'block' : 'none' }"
        ></div>
      </div>

      <!-- Pages -->
      <div
        v-for="i in TOTAL_PAGES"
        :key="i - 1"
        class="page"
        :class="{ flipped: isFlipped(i - 1) }"
        :style="{ zIndex: pageZIndex(i - 1) }"
        @click="handlePageClick(i - 1)"
      >
        <!-- FRONT FACE -->
        <div class="page-front">

          <!-- PAGE 0 FRONT: Cover -->
          <template v-if="i - 1 === 0">
            <div class="jump-bar">
              <span style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:3px;color:#fff;">JUMP <span style="color:#c8942a;">COMICS</span> ✦</span>
              <span style="font-family:'Bangers',cursive;font-size:0.5rem;letter-spacing:2px;color:#c8942a;">FULL STACK ED.</span>
            </div>
            <div style="flex:1;position:relative;overflow:hidden;background:#fff;">
              <div class="gold-frame"></div>
              <div style="position:absolute;top:10px;left:10px;right:10px;bottom:10px;overflow:hidden;">
                <img :src="coverImg" style="width:100%;height:100%;object-fit:cover;object-position:center top;" alt="Jorge Aguilar" />
                <div style="position:absolute;inset:0;background:linear-gradient(to bottom,transparent 60%,rgba(0,0,0,0.4) 100%);"></div>
              </div>
            </div>
            <div style="background:#0a0a0a;border-top:4px solid #c8942a;padding:0.65rem 0.9rem 0.55rem;flex-shrink:0;">
              <span style="font-family:'Bangers',cursive;font-size:0.58rem;letter-spacing:3px;color:#c8942a;display:block;">巻ノ一 · VOLUME 01</span>
              <span style="font-family:'Bangers',cursive;font-size:2.5rem;color:#fff;line-height:0.9;letter-spacing:4px;display:block;text-shadow:3px 3px 0 #c8942a,-1px -1px 0 #333;">JORGE<br>AGUILAR</span>
              <span style="font-family:'Bangers',cursive;font-size:0.65rem;letter-spacing:4px;color:#c8942a;display:block;margin-top:0.2rem;">— FULL STACK DEVELOPER —</span>
            </div>
          </template>

          <!-- PAGE 1 FRONT: Chapter 01 Hero Introduction -->
          <template v-else-if="i - 1 === 1">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 01 — HERO INTRODUCTION</div>
              <div style="flex:1;padding:0.5rem;display:flex;flex-direction:column;gap:4px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.6rem;font-size:0.65rem;font-weight:900;line-height:1.5;color:#111;font-style:italic;">"A full stack developer forging platforms used by over 1 million users. React, Vue, NestJS, Golang — all weapons mastered."</div>
                <div style="display:grid;grid-template-columns:1fr 1fr;gap:2px;flex:0 0 58px;">
                  <div style="border:2px solid #0a0a0a;background:#0a0a0a;display:flex;flex-direction:column;align-items:center;justify-content:center;"><span style="font-family:'Bangers',cursive;font-size:1.4rem;color:#fff;line-height:1;">4+</span><span style="font-size:0.42rem;color:#888;letter-spacing:1px;text-transform:uppercase;">YRS</span></div>
                  <div style="border:2px solid #0a0a0a;background:#0a0a0a;display:flex;flex-direction:column;align-items:center;justify-content:center;"><span style="font-family:'Bangers',cursive;font-size:1.4rem;color:#fff;line-height:1;">8+</span><span style="font-size:0.42rem;color:#888;letter-spacing:1px;text-transform:uppercase;">PROJECTS</span></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.45rem;flex:1;overflow:hidden;">
                  <div style="font-family:'Bangers',cursive;font-size:0.52rem;letter-spacing:2px;margin-bottom:0.28rem;">TECH ARSENAL</div>
                  <span class="tag">React</span><span class="tag">Vue</span><span class="tag">NestJS</span><span class="tag">Golang</span><span class="tag">Next.js</span><span class="tag">AWS</span><span class="tag">Docker</span><span class="tag">PostgreSQL</span><span class="tag">TypeScript</span>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 2 FRONT: Chapter 02 Battle Log (part 2) -->
          <template v-else-if="i - 1 === 2">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">&nbsp;</div>
              <div style="border:2px solid #0a0a0a;background:#0a0a0a;padding:0.4rem 0.55rem;flex-shrink:0;">
                <div style="font-family:'Bangers',cursive;font-size:0.9rem;letter-spacing:2px;color:#fff;">CODE ÉXITOS</div>
                <div style="font-family:'Bangers',cursive;font-size:0.48rem;letter-spacing:3px;color:#555;">SOFTWARE DEVELOPER · 2022–2025</div>
              </div>
              <div style="flex:1;padding:0.4rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ EV RENTAL &amp; SALES PLATFORM</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Designed and developed a platform for renting and selling electric vehicles, integrating payments and improving the infrastructure to provide efficient and secure services.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ MEDICAL EVENTS APPS</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Implemented interactive applications for medical events, integrating multimedia content and quizzes to increase attendee engagement.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ TIME-TRACKING MIGRATION</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Worked on migrating a time-tracking web application for a U.S. client, contributing to the platform's growth from 100,000 to over 1 million users.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ GITHUB ACTIONS DASHBOARD</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Developed an internal analytics dashboard to monitor GitHub Actions across all company repositories, providing centralized insights into CI/CD workflows, execution status, and performance metrics.</div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 3 FRONT: Chapter 03 Skill Tree -->
          <template v-else-if="i - 1 === 3">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">&nbsp;</div>
              <div style="flex:1;padding:0.4rem;display:flex;flex-direction:column;gap:2px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">FRONTEND</div>
                  <div class="sr"><span class="sn">React</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Vue</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Next.js</span><div class="st"><div class="sf" style="width:82%"></div></div></div>
                  <div class="sr"><span class="sn">Tailwind</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">BACKEND</div>
                  <div class="sr"><span class="sn">NestJS</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Node.js</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Golang</span><div class="st"><div class="sf" style="width:70%"></div></div></div>
                  <div class="sr"><span class="sn">.NET</span><div class="st"><div class="sf" style="width:65%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;flex:1;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">CLOUD / CI·CD / AI</div>
                  <div class="sr"><span class="sn">AWS</span><div class="st"><div class="sf" style="width:80%"></div></div></div>
                  <div class="sr"><span class="sn">Docker</span><div class="st"><div class="sf" style="width:76%"></div></div></div>
                  <div class="sr"><span class="sn">GH Actions</span><div class="st"><div class="sf" style="width:78%"></div></div></div>
                  <div class="sr"><span class="sn">Claude</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Cursor</span><div class="st"><div class="sf" style="width:80%"></div></div></div>
                  <div class="sr"><span class="sn">Copilot</span><div class="st"><div class="sf" style="width:72%"></div></div></div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 4 FRONT: Chapter 04 Training Arc (part 2) -->
          <template v-else-if="i - 1 === 4">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">&nbsp;</div>
              <div style="flex:1;overflow:hidden;background:#000;position:relative;">
                <img :src="originArcImg" style="width:100%;height:100%;object-fit:cover;" alt="Origin Arc" />
              </div>
            </div>
          </template>

        </div>

        <!-- BACK FACE -->
        <div class="page-back">

          <!-- PAGE 0 BACK: Table of Contents -->
          <template v-if="i - 1 === 0">
            <div style="display:flex;flex-direction:column;height:100%;background:#faf7f2;">
              <div style="padding:1.2rem;flex:1;display:flex;flex-direction:column;justify-content:center;align-items:center;gap:1rem;">
                <div style="font-family:'Bangers',cursive;font-size:0.65rem;letter-spacing:4px;color:#888;margin-bottom:0.5rem;">TABLE OF CONTENTS</div>
                <div style="width:100%;display:flex;flex-direction:column;gap:2px;">
                  <div style="border:2px solid #0a0a0a;padding:0.5rem 0.8rem;display:flex;align-items:center;justify-content:space-between;"><span style="font-family:'Bangers',cursive;font-size:0.85rem;letter-spacing:2px;">CH.01 — HERO INTRO</span><span style="font-family:'Bangers',cursive;font-size:0.65rem;color:#888;">P.2</span></div>
                  <div style="border:2px solid #0a0a0a;padding:0.5rem 0.8rem;display:flex;align-items:center;justify-content:space-between;"><span style="font-family:'Bangers',cursive;font-size:0.85rem;letter-spacing:2px;">CH.02 — BATTLE LOG</span><span style="font-family:'Bangers',cursive;font-size:0.65rem;color:#888;">P.4</span></div>
                  <div style="border:2px solid #0a0a0a;padding:0.5rem 0.8rem;display:flex;align-items:center;justify-content:space-between;"><span style="font-family:'Bangers',cursive;font-size:0.85rem;letter-spacing:2px;">CH.03 — SKILL TREE</span><span style="font-family:'Bangers',cursive;font-size:0.65rem;color:#888;">P.6</span></div>
                  <div style="border:2px solid #0a0a0a;padding:0.5rem 0.8rem;display:flex;align-items:center;justify-content:space-between;"><span style="font-family:'Bangers',cursive;font-size:0.85rem;letter-spacing:2px;">CH.04 — ORIGIN ARC</span><span style="font-family:'Bangers',cursive;font-size:0.65rem;color:#888;">P.8</span></div>
                  <div style="border:2px solid #0a0a0a;padding:0.5rem 0.8rem;display:flex;align-items:center;justify-content:space-between;"><span style="font-family:'Bangers',cursive;font-size:0.85rem;letter-spacing:2px;">FINAL — CONTACT</span><span style="font-family:'Bangers',cursive;font-size:0.65rem;color:#888;">P.10</span></div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 1 BACK: Chapter 02 Battle Log (part 1) -->
          <template v-else-if="i - 1 === 1">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 02 — BATTLE LOG</div>
              <div style="border:2px solid #0a0a0a;background:#0a0a0a;padding:0.4rem 0.55rem;flex-shrink:0;">
                <div style="font-family:'Bangers',cursive;font-size:0.9rem;letter-spacing:2px;color:#fff;">CODE ÉXITOS</div>
                <div style="font-family:'Bangers',cursive;font-size:0.48rem;letter-spacing:3px;color:#555;">SOFTWARE DEVELOPER · 2022–2025</div>
              </div>
              <div style="flex:1;padding:0.4rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ JOB PLACEMENT PLATFORM</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Developed and optimized a scalable web platform for a U.S. client used by over 500,000 users, designed to streamline job placement by connecting professionals with hiring companies.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ VEHICLE SALES PLATFORM</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Worked on a vehicle sales platform for a Canadian client, enhancing the user experience and optimizing the overall performance of the application.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ ENTERPRISE MANAGEMENT PLATFORM</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Contributed to an internal enterprise platform to centralize company data, and built analytics dashboards to visualize key business metrics and support data-driven decision-making.</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.5rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-size:0.58rem;font-weight:900;color:#0a0a0a;margin-bottom:0.18rem;">◆ EVENT REGISTRATION MOBILE APP</div>
                  <div style="font-size:0.54rem;line-height:1.45;color:#333;">Created a mobile application for event attendee registration, incorporating barcode scanning and manual data entry for an improved user experience.</div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 2 BACK: Chapter 03 Skill Tree (part 1) -->
          <template v-else-if="i - 1 === 2">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 03 — SKILL TREE</div>
              <div style="flex:1;padding:0.4rem;display:flex;flex-direction:column;gap:2px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">DATABASES</div>
                  <div class="sr"><span class="sn">PostgreSQL</span><div class="st"><div class="sf" style="width:82%"></div></div></div>
                  <div class="sr"><span class="sn">MongoDB</span><div class="st"><div class="sf" style="width:75%"></div></div></div>
                  <div class="sr"><span class="sn">MySQL</span><div class="st"><div class="sf" style="width:70%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">TESTING</div>
                  <div class="sr"><span class="sn">Cypress</span><div class="st"><div class="sf" style="width:68%"></div></div></div>
                  <div class="sr"><span class="sn">Playwright</span><div class="st"><div class="sf" style="width:65%"></div></div></div>
                  <div class="sr"><span class="sn">Jest</span><div class="st"><div class="sf" style="width:70%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.35rem;">LANGUAGES</div>
                  <div style="display:grid;grid-template-columns:1fr 1fr;gap:3px;">
                    <div style="border:2px solid #0a0a0a;padding:0.35rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:0.92rem;">TS</div><div style="font-size:0.48rem;font-weight:700;">TypeScript</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.35rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:0.92rem;">JS</div><div style="font-size:0.48rem;font-weight:700;">JavaScript</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.35rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:0.92rem;">GO</div><div style="font-size:0.48rem;font-weight:700;">Golang</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.35rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:0.92rem;">C#</div><div style="font-size:0.48rem;font-weight:700;">C Sharp</div></div>
                  </div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 3 BACK: Training Arc / Education -->
          <template v-else-if="i - 1 === 3">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 04 — TRAINING ARC</div>
              <div style="flex:1;padding:0.45rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UNITEC</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Mechatronics Engineering</div><div style="font-size:0.56rem;color:#888;font-weight:700;">2012–2017</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UNITEC</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Master's in Finance</div><div style="font-size:0.56rem;color:#888;font-weight:700;">2018–2021</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">CODECADEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Full-Stack Engineer Career Path</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UDEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">C# Language Course</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">CODECADEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Learn JavaScript Course</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">CODECADEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Learn HTML Course</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;flex:1;display:flex;flex-direction:column;justify-content:center;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">CODECADEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Learn Responsive Design Course</div></div>
              </div>
            </div>
          </template>

          <!-- PAGE 4 BACK: Final Chapter — Contact -->
          <template v-else-if="i - 1 === 4">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">FINAL CHAPTER — CONTACT</div>
              <div style="flex:1;display:flex;flex-direction:column;overflow:hidden;">
                <div style="padding:0.5rem;border-bottom:1.5px solid #ddd;flex-shrink:0;">
                  <div style="border:1.5px solid #0a0a0a;border-radius:50%/40%;padding:0.38rem 0.65rem;display:inline-block;font-size:0.6rem;font-weight:900;line-height:1.3;color:#0a0a0a;">"Ready for the next arc."</div>
                </div>
                <div style="flex:1;overflow:hidden;">
                  <div class="crow"><span class="clbl">EMAIL</span><span class="cval">georgeaguilar11@hotmail.com</span></div>
                  <div class="crow"><span class="clbl">PHONE</span><span class="cval">+504 9570 0336</span></div>
                  <div class="crow"><span class="clbl">LINKEDIN</span><span class="cval">linkedin.com/in/jaguilardev</span></div>
                  <div class="crow"><span class="clbl">GITHUB</span><span class="cval">github.com/georgeaguilar</span></div>
                  <div class="crow"><span class="clbl">LOCATION</span><span class="cval">San Pedro Sula, HN</span></div>
                </div>
                <div style="padding:1rem;text-align:center;border-top:1.5px solid #ddd;position:relative;overflow:hidden;">
                  <div style="font-family:'Bangers',cursive;font-size:3rem;color:#0a0a0a;opacity:0.06;position:absolute;right:-5px;bottom:-5px;line-height:0.85;letter-spacing:3px;">END</div>
                  <div style="font-family:'Bangers',cursive;font-size:1.5rem;letter-spacing:4px;color:#0a0a0a;position:relative;z-index:1;">TO BE<br>CONTINUED</div>
                  <div style="font-family:'Bangers',cursive;font-size:0.55rem;letter-spacing:3px;color:#c8942a;border:1.5px solid #c8942a;padding:0.25rem 0.7rem;display:inline-block;margin-top:0.5rem;position:relative;z-index:1;">VOL.2 COMING SOON</div>
                </div>
              </div>
            </div>
          </template>

        </div>
      </div>

    </div>
  </div>
  </div>

  <!-- Navigation -->
  <div class="nav-arrows">
    <button class="arrow-btn" :disabled="isPrevDisabled" @click="prevPage">◀ PREV</button>
    <span class="page-indicator">{{ indicatorText }}</span>
    <button class="arrow-btn" :disabled="isNextDisabled" @click="nextPage">NEXT ▶</button>
  </div>
  <button class="back-shelf-btn" @click="goToShelf">← BACK TO SHELF</button>
</div>

</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Bangers&family=Noto+Sans+JP:wght@400;700;900&display=swap');
</style>

<style scoped>
body {
  background: #1a1a2e;
  font-family: 'Noto Sans JP', sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  user-select: none;
}

:global(body) {
  background: #1a1a2e;
  font-family: 'Noto Sans JP', sans-serif;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  user-select: none;
}

:global(body::before) {
  content: '';
  position: fixed;
  inset: 0;
  background-image: radial-gradient(circle, rgba(255,255,255,0.04) 1px, transparent 1px);
  background-size: 28px 28px;
  pointer-events: none;
}

:global(#app) {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  width: 100%;
}

.book-shell {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.scene-wrapper {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}


.hint-bar {
  font-family: 'Bangers', cursive;
  font-size: 0.6rem;
  letter-spacing: 3px;
  color: rgba(255,255,255,0.2);
  margin-bottom: 0.8rem;
  text-align: center;
}

.scene {
  perspective: 1600px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}

.book {
  position: relative;
  width: 300px;
  height: 520px;
  transform-style: preserve-3d;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
}

/* Back cover */
.back-cover {
  position: absolute;
  left: 0; top: 0;
  width: 300px; height: 520px;
  background: #fff;
  border: 2px solid #1a1a1a;
  overflow: hidden;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center; gap: 0.8rem;
  transform: translateZ(-2px);
}
.back-cover::before {
  content: '';
  position: absolute; inset: 0;
  background-image: radial-gradient(circle, #333 0.5px, transparent 0.5px);
  background-size: 5px 5px; opacity: 0.25;
}
.bc-label {
  font-family: 'Bangers', cursive;
  font-size: 0.62rem; letter-spacing: 4px; color: #888;
  position: relative; z-index: 1;
}
.bc-barcode {
  display: flex; gap: 1.5px;
  position: relative; z-index: 1; margin-top: 0.8rem;
}
.bc-isbn {
  font-family: monospace; font-size: 0.42rem; color: #888;
  position: relative; z-index: 1; margin-top: 0.5rem;
}

/* Spine */
.spine {
  position: absolute;
  left: -18px; top: 0;
  width: 18px; height: 520px;
  background: linear-gradient(to right, #080808, #1c1c1c, #080808);
  transform-origin: right center;
  transform: rotateY(-90deg);
  display: flex; flex-direction: column;
  align-items: center; justify-content: center; gap: 0.4rem;
  border: 1px solid #2a2a2a;
  overflow: hidden;
}
.spine-title {
  font-family: 'Bangers', cursive;
  font-size: 0.65rem; letter-spacing: 2px; color: #c8942a;
  writing-mode: vertical-rl; transform: rotate(180deg);
}
.spine-bar {
  width: 1px; height: 28px; background: #c8942a; opacity: 0.4;
}
.spine-sub {
  font-family: 'Bangers', cursive;
  font-size: 0.48rem; letter-spacing: 1.5px; color: #555;
  writing-mode: vertical-rl; transform: rotate(180deg);
}
.spine-vol {
  font-family: 'Bangers', cursive;
  font-size: 0.55rem; letter-spacing: 1px; color: #444;
  writing-mode: vertical-rl; transform: rotate(180deg);
}

/* Page edges */
.page-edges {
  position: absolute;
  right: -8px; top: 3px;
  width: 8px; height: 514px;
  display: flex; flex-direction: column;
  overflow: hidden;
}
.page-edge-strip {
  flex: 1;
  background: linear-gradient(to right, #e8e3db, #f0ebe2);
  border-top: 0.5px solid #d5d0c8;
}

/* Pages */
.page {
  position: absolute;
  left: 0; top: 0;
  width: 300px; height: 520px;
  transform-origin: left center;
  transform-style: preserve-3d;
  transition: transform 0.8s cubic-bezier(0.645, 0.045, 0.355, 1.000);
  cursor: pointer;
}
.page-front, .page-back {
  position: absolute;
  inset: 0;
  backface-visibility: hidden;
  overflow: hidden;
  border: 2.5px solid #0a0a0a;
  display: flex;
  flex-direction: column;
}
.page-front { border-left: none; }
.page-back  { border-left: none; transform: rotateY(180deg); }

.page.flipped {
  transform: rotateY(-180deg);
}

/* Shared content styles */
.jump-bar {
  background: #0a0a0a;
  border-bottom: 3px solid #c8942a;
  padding: 0.28rem 0.7rem;
  display: flex; align-items: center; justify-content: space-between;
  flex-shrink: 0;
}
.gold-frame {
  position: absolute; inset: 8px;
  border: 2px solid #c8942a;
  pointer-events: none; z-index: 10;
}
.gold-frame::after {
  content: '';
  position: absolute; inset: 4px;
  border: 1px solid rgba(200,148,42,0.35);
}
.title-bar {
  position: absolute;
  bottom: 0; left: 10px; right: 10px;
  background: #0a0a0a;
  border-top: 4px solid #c8942a;
  padding: 0.65rem 0.9rem 0.55rem;
  z-index: 20;
}
.ch {
  background: #0a0a0a;
  padding: 0.25rem 0.7rem;
  font-family: 'Bangers', cursive;
  font-size: 0.52rem; letter-spacing: 3px; color: #fff;
  border-bottom: 2px solid #0a0a0a;
  flex-shrink: 0;
}
.tag {
  display: inline-block; background: #0a0a0a; color: #fff;
  font-size: 0.5rem; font-weight: 700; letter-spacing: 0.5px;
  padding: 0.08rem 0.3rem; margin: 0.06rem; font-family: monospace;
}
.sr { display: flex; align-items: center; gap: 0.3rem; margin-bottom: 0.2rem; }
.sn { font-size: 0.56rem; font-weight: 700; flex: 0 0 60px; }
.st { flex: 1; height: 4px; background: #e8e8e8; border: 1px solid #ccc; overflow: hidden; }
.sf { height: 100%; background: #0a0a0a; }
.crow { display: flex; align-items: stretch; border-bottom: 1.5px solid #1a1a1a; }
.crow:last-child { border-bottom: none; }
.clbl {
  font-family: 'Bangers', cursive; font-size: 0.52rem; letter-spacing: 1.5px;
  background: #0a0a0a; color: #fff; padding: 0.48rem; min-width: 64px;
  display: flex; align-items: center; border-right: 2px solid #0a0a0a;
}
.cval { padding: 0.48rem 0.58rem; font-size: 0.56rem; font-weight: 700; display: flex; align-items: center; word-break: break-all; }

/* Navigation */
.nav-arrows {
  display: flex; align-items: center; gap: 1.5rem;
  margin-top: 1rem;
  position: relative; z-index: 10;
}
.arrow-btn {
  font-family: 'Bangers', cursive; font-size: 0.8rem; letter-spacing: 2px;
  background: none; border: 1.5px solid rgba(255,255,255,0.2);
  color: rgba(255,255,255,0.5); padding: 0.38rem 1.1rem;
  cursor: pointer; transition: all 0.18s;
}
.arrow-btn:hover { border-color: #c8942a; color: #c8942a; }
.arrow-btn:disabled { opacity: 0.2; cursor: default; }
.page-indicator {
  font-family: 'Bangers', cursive; font-size: 0.65rem; letter-spacing: 3px;
  color: rgba(255,255,255,0.3);
}
.back-shelf-btn {
  font-family: 'Bangers', cursive; font-size: 0.6rem; letter-spacing: 2px;
  background: none; border: none;
  color: rgba(255,255,255,0.2);
  cursor: pointer; margin-top: 0.3rem;
  transition: color 0.18s;
}
.back-shelf-btn:hover { color: #c8942a; }

/* ── SHELF SCENE ── */
.shelf-scene {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  gap: 1rem;
}
.shelf-scene.shelf-leaving {
  animation: shelf-exit 0.9s forwards;
  pointer-events: none;
}
@keyframes shelf-exit {
  0%   { opacity: 1; transform: scale(1); }
  50%  { opacity: 1; transform: scale(1.02); }
  100% { opacity: 0; transform: scale(1.06); }
}

.shelf-header { text-align: center; }

.bookshelf-wrap {
  perspective: 1100px;
}
.bookcase {
  width: 420px;
  border-left: 18px solid #8a5c1a;
  border-right: 18px solid #8a5c1a;
  border-top: 16px solid #a07220;
  border-bottom: 22px solid #6a4412;
  background: #b8955a;
  display: flex;
  flex-direction: column;
  box-shadow:
    10px 18px 48px rgba(0,0,0,0.7),
    inset 4px 0 14px rgba(0,0,0,0.25),
    inset -4px 0 14px rgba(0,0,0,0.25),
    inset 0 4px 12px rgba(0,0,0,0.18);
  transform: rotateX(4deg);
  transform-style: preserve-3d;
}
.shelf-section {
  flex: 1;
  min-height: 140px;
  display: flex;
  align-items: flex-end;
  padding: 0 6px;
  overflow: visible;
}
.shelf-section.shelf-mid {
  min-height: 148px;
}
.books-area {
  display: flex;
  align-items: flex-end;
  gap: 3px;
  width: 100%;
  overflow: visible;
}
.shelf-divider {
  height: 16px;
  background: linear-gradient(to bottom, #c09030 0%, #8a5c18 45%, #a07022 100%);
  box-shadow: 0 5px 14px rgba(0,0,0,0.45), inset 0 1px 0 rgba(255,255,255,0.14), inset 0 -1px 0 rgba(0,0,0,0.22);
  flex-shrink: 0;
}
.s-book {
  border-radius: 1px;
  flex-shrink: 0;
  box-shadow: inset -3px 0 5px rgba(0,0,0,0.45), 2px 2px 8px rgba(0,0,0,0.55);
}
.manga-hit {
  align-self: stretch;
  display: flex;
  align-items: flex-end;
  cursor: pointer;
  flex-shrink: 0;
}
.manga-spine {
  width: 26px;
  height: 115px;
  background: linear-gradient(to right, #050505, #1c1c1c, #080808);
  border: 1px solid #3a3a3a;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 4px;
  flex-shrink: 0;
  position: relative;
  box-shadow:
    0 0 18px rgba(200,148,42,0.35),
    inset -3px 0 6px rgba(0,0,0,0.4),
    3px 3px 10px rgba(0,0,0,0.6);
  overflow: hidden;
  transition: transform 0.25s ease, box-shadow 0.25s ease;
}
.manga-spine::before {
  content: '';
  position: absolute;
  inset: 0;
  border: 1px solid rgba(200,148,42,0.2);
  pointer-events: none;
  z-index: 1;
}
.manga-spine::after {
  content: '';
  position: absolute;
  inset: 0;
  background: linear-gradient(
    105deg,
    transparent 30%,
    rgba(255,255,255,0.45) 50%,
    transparent 70%
  );
  transform: translateX(-150%);
  pointer-events: none;
}
.manga-spine.spine-hover {
  transform: translateY(-9px);
  box-shadow:
    0 12px 24px rgba(0,0,0,0.6),
    0 0 16px rgba(200,148,42,0.4);
}
.manga-spine.spine-hover::after {
  transform: translateX(250%);
  transition: transform 0.55s ease;
}
.manga-spine.manga-fly {
  animation: manga-fly-off 0.9s cubic-bezier(0.4,0,0.2,1) forwards;
}
@keyframes manga-fly-off {
  0%   { transform: translateY(-18px) scale(1.06); opacity: 1; }
  25%  { transform: translateY(-50px) scale(1.15); opacity: 1; }
  60%  { transform: translateY(-160px) scale(2.2) rotateY(30deg); opacity: 0.8; }
  100% { transform: translateY(-380px) scale(6) rotateY(90deg); opacity: 0; }
}

.ms-title {
  font-family: 'Bangers', cursive;
  font-size: 0.5rem; letter-spacing: 1.5px; color: #c8942a;
  writing-mode: vertical-rl; transform: rotate(180deg);
}
.ms-bar { width: 1px; height: 18px; background: #c8942a; opacity: 0.5; }
.ms-sub {
  font-family: 'Bangers', cursive;
  font-size: 0.35rem; letter-spacing: 1px; color: #555;
  writing-mode: vertical-rl; transform: rotate(180deg);
}
.ms-vol {
  font-family: 'Bangers', cursive;
  font-size: 0.4rem; letter-spacing: 1px; color: #444;
  writing-mode: vertical-rl; transform: rotate(180deg);
}

.shelf-plank {
  width: calc(100% + 48px);
  height: 18px;
  background: linear-gradient(to bottom, #b08030, #7a5520, #9a7028);
  box-shadow: 0 6px 18px rgba(0,0,0,0.7), inset 0 1px 0 rgba(255,255,255,0.08);
  border-radius: 2px;
  transform: rotateX(10deg);
  transform-origin: top center;
}

.shelf-hint {
  font-family: 'Bangers', cursive;
  font-size: 0.55rem; letter-spacing: 4px;
  color: rgba(255,255,255,0.22);
  animation: hint-blink 2.5s ease-in-out infinite;
}
@keyframes hint-blink {
  0%, 100% { opacity: 0.25; }
  50%       { opacity: 0.8; }
}

/* ── CONFIRM DIALOG ── */
.confirm-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.72);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 100;
}
.confirm-box {
  background: #fff;
  border: 3px solid #0a0a0a;
  padding: 1.8rem 2.2rem;
  text-align: center;
  position: relative;
  box-shadow: 8px 8px 0 #0a0a0a;
}
.confirm-box::before {
  content: '';
  position: absolute;
  inset: 5px;
  border: 1.5px solid rgba(200,148,42,0.4);
  pointer-events: none;
}
.confirm-btn-yes {
  font-family: 'Bangers', cursive;
  font-size: 0.9rem; letter-spacing: 2px;
  background: #0a0a0a; color: #c8942a;
  border: 2px solid #0a0a0a;
  padding: 0.5rem 1.2rem;
  cursor: pointer; transition: all 0.15s;
}
.confirm-btn-yes:hover { background: #c8942a; color: #0a0a0a; border-color: #c8942a; }
.confirm-btn-no {
  font-family: 'Bangers', cursive;
  font-size: 0.9rem; letter-spacing: 2px;
  background: none; color: #0a0a0a;
  border: 2px solid #0a0a0a;
  padding: 0.5rem 1.2rem;
  cursor: pointer; transition: all 0.15s;
}
.confirm-btn-no:hover { background: #0a0a0a; color: #fff; }

/* ── TRANSITION: pop ── */
.pop-enter-active { animation: pop-in 0.25s cubic-bezier(0.34,1.56,0.64,1); }
.pop-leave-active { animation: pop-in 0.18s reverse ease-in; }
@keyframes pop-in {
  from { opacity: 0; transform: scale(0.82); }
  to   { opacity: 1; transform: scale(1); }
}

/* ── READER appear ── */
.reader-appear {
  animation: reader-fade-in 0.55s ease forwards;
}
@keyframes reader-fade-in {
  from { opacity: 0; }
  to   { opacity: 1; }
}
</style>
