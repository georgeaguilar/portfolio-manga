<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import coverImg from '../assets/cover.png'
import originArcImg from '../assets/origin-arc.png'

const TOTAL_PAGES = 5
const currentPage = ref(0)

const pageLabels = ['COVER', 'PAGE 1', 'PAGE 2', 'PAGE 3', 'PAGE 4', 'END']
const indicatorText = computed(() => `${pageLabels[currentPage.value]} / ${TOTAL_PAGES}`)
const isPrevDisabled = computed(() => currentPage.value === 0)
const isNextDisabled = computed(() => currentPage.value === TOTAL_PAGES)

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
  if (currentPage.value >= TOTAL_PAGES) return
  currentPage.value++
}
function prevPage() {
  if (currentPage.value <= 0) return
  currentPage.value--
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

<template>
  <div class="hint-bar">◀ PREV PAGE — CLICK COVER TO OPEN — NEXT PAGE ▶</div>

  <div class="scene">
    <div class="book">

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
                <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:2px;flex:0 0 58px;">
                  <div style="border:2px solid #0a0a0a;background:#0a0a0a;display:flex;flex-direction:column;align-items:center;justify-content:center;"><span style="font-family:'Bangers',cursive;font-size:1.4rem;color:#fff;line-height:1;">3+</span><span style="font-size:0.42rem;color:#888;letter-spacing:1px;text-transform:uppercase;">YRS</span></div>
                  <div style="border:2px solid #0a0a0a;background:#0a0a0a;display:flex;flex-direction:column;align-items:center;justify-content:center;"><span style="font-family:'Bangers',cursive;font-size:1.4rem;color:#fff;line-height:1;">1M+</span><span style="font-size:0.42rem;color:#888;letter-spacing:1px;text-transform:uppercase;">USERS</span></div>
                  <div style="border:2px solid #0a0a0a;background:#0a0a0a;display:flex;flex-direction:column;align-items:center;justify-content:center;"><span style="font-family:'Bangers',cursive;font-size:1.4rem;color:#fff;line-height:1;">8+</span><span style="font-size:0.42rem;color:#888;letter-spacing:1px;text-transform:uppercase;">PROJECTS</span></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.45rem;flex:1;overflow:hidden;">
                  <div style="font-family:'Bangers',cursive;font-size:0.52rem;letter-spacing:2px;margin-bottom:0.28rem;">TECH ARSENAL</div>
                  <span class="tag">React</span><span class="tag">Vue</span><span class="tag">NestJS</span><span class="tag">Golang</span><span class="tag">Next.js</span><span class="tag">AWS</span><span class="tag">Docker</span><span class="tag">PostgreSQL</span><span class="tag">TypeScript</span>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 2 FRONT: Chapter 02 Battle Log -->
          <template v-else-if="i - 1 === 2">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 02 — BATTLE LOG</div>
              <div style="flex:1;padding:0.45rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;background:#0a0a0a;padding:0.55rem;flex-shrink:0;">
                  <div style="font-family:'Bangers',cursive;font-size:1rem;letter-spacing:2px;color:#fff;">CODE ÉXITOS</div>
                  <div style="font-family:'Bangers',cursive;font-size:0.55rem;letter-spacing:3px;color:#555;margin-top:0.1rem;">SOFTWARE DEVELOPER · 2022–2025</div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.45rem;flex:1;position:relative;overflow:hidden;">
                  <div style="position:absolute;inset:0;background-image:radial-gradient(circle,#bbb 0.4px,transparent 0.4px);background-size:4px 4px;opacity:0.09;"></div>
                  <ul style="list-style:none;display:grid;gap:0.28rem;position:relative;">
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>Job placement platform — <b>500,000+ users</b></li>
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>Time-tracking migration: <b>100K → 1M+ users</b></li>
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>EV rental platform + payment gateway</li>
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>GitHub Actions analytics dashboard</li>
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>Mobile event app + barcode scanning</li>
                    <li style="font-size:0.6rem;line-height:1.4;padding-left:0.9rem;position:relative;"><span style="position:absolute;left:0;font-size:0.4rem;top:0.26rem;">◆</span>Vehicle sales platform — Canada</li>
                  </ul>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.38rem 0.45rem;flex-shrink:0;">
                  <span class="tag">React</span><span class="tag">Vue</span><span class="tag">NestJS</span><span class="tag">Golang</span><span class="tag">Next.js</span><span class="tag">AWS</span><span class="tag">Azure</span><span class="tag">Docker</span>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 3 FRONT: Chapter 03 Skill Tree -->
          <template v-else-if="i - 1 === 3">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 03 — SKILL TREE</div>
              <div style="flex:1;padding:0.4rem;display:flex;flex-direction:column;gap:2px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">FRONTEND</div>
                  <div class="sr"><span class="sn">React</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Vue</span><div class="st"><div class="sf" style="width:85%"></div></div></div>
                  <div class="sr"><span class="sn">Next.js</span><div class="st"><div class="sf" style="width:80%"></div></div></div>
                  <div class="sr"><span class="sn">Tailwind</span><div class="st"><div class="sf" style="width:88%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">BACKEND</div>
                  <div class="sr"><span class="sn">NestJS</span><div class="st"><div class="sf" style="width:90%"></div></div></div>
                  <div class="sr"><span class="sn">Node.js</span><div class="st"><div class="sf" style="width:85%"></div></div></div>
                  <div class="sr"><span class="sn">Golang</span><div class="st"><div class="sf" style="width:70%"></div></div></div>
                  <div class="sr"><span class="sn">.NET</span><div class="st"><div class="sf" style="width:65%"></div></div></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.42rem 0.52rem;flex:1;">
                  <div style="font-family:'Bangers',cursive;font-size:0.7rem;letter-spacing:2px;border-bottom:2px solid #0a0a0a;padding-bottom:0.18rem;margin-bottom:0.32rem;">CLOUD / DB / TESTING</div>
                  <div class="sr"><span class="sn">AWS</span><div class="st"><div class="sf" style="width:80%"></div></div></div>
                  <div class="sr"><span class="sn">Docker</span><div class="st"><div class="sf" style="width:76%"></div></div></div>
                  <div class="sr"><span class="sn">PostgreSQL</span><div class="st"><div class="sf" style="width:82%"></div></div></div>
                  <div class="sr"><span class="sn">Cypress</span><div class="st"><div class="sf" style="width:68%"></div></div></div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 4 FRONT: Chapter 04 Origin Arc -->
          <template v-else-if="i - 1 === 4">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 04 — ORIGIN ARC</div>
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

          <!-- PAGE 1 BACK: Power Levels / Impact Stats -->
          <template v-else-if="i - 1 === 1">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 01 — POWER LEVELS</div>
              <div style="flex:1;padding:0.45rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;background:#0a0a0a;padding:0.9rem;display:grid;grid-template-columns:1fr 1fr 1fr;gap:0.4rem;flex-shrink:0;position:relative;overflow:hidden;">
                  <div style="font-family:'Bangers',cursive;font-size:2.5rem;color:#fff;opacity:0.05;position:absolute;right:-5px;bottom:-8px;">POWER!</div>
                  <div style="text-align:center;position:relative;z-index:1;"><span style="font-family:'Bangers',cursive;font-size:1.5rem;color:#fff;display:block;line-height:1;">500K+</span><span style="font-size:0.42rem;color:#555;letter-spacing:1.5px;text-transform:uppercase;display:block;margin-top:0.1rem;">Users</span></div>
                  <div style="text-align:center;position:relative;z-index:1;"><span style="font-family:'Bangers',cursive;font-size:1.5rem;color:#fff;display:block;line-height:1;">10x</span><span style="font-size:0.42rem;color:#555;letter-spacing:1.5px;text-transform:uppercase;display:block;margin-top:0.1rem;">Growth</span></div>
                  <div style="text-align:center;position:relative;z-index:1;"><span style="font-family:'Bangers',cursive;font-size:1.5rem;color:#fff;display:block;line-height:1;">8+</span><span style="font-size:0.42rem;color:#555;letter-spacing:1.5px;text-transform:uppercase;display:block;margin-top:0.1rem;">Projects</span></div>
                </div>
                <div style="border:2px solid #0a0a0a;padding:0.45rem;flex:1;">
                  <div style="display:grid;grid-template-columns:1fr 1fr;gap:3px;">
                    <div style="border:2px solid #0a0a0a;padding:0.5rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:1rem;">3</div><div style="font-size:0.48rem;font-weight:700;color:#555;">COUNTRIES</div><div style="font-size:0.44rem;color:#999;">USA · CANADA · HN</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.5rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:1rem;">3+</div><div style="font-size:0.48rem;font-weight:700;color:#555;">YEARS</div><div style="font-size:0.44rem;color:#999;">2022–2025</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.5rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:1rem;">12+</div><div style="font-size:0.48rem;font-weight:700;color:#555;">FRAMEWORKS</div></div>
                    <div style="border:2px solid #0a0a0a;padding:0.5rem;text-align:center;"><div style="font-family:'Bangers',cursive;font-size:1rem;">∞</div><div style="font-size:0.48rem;font-weight:700;color:#555;">COMMITS</div></div>
                  </div>
                </div>
              </div>
            </div>
          </template>

          <!-- PAGE 2 BACK: Arsenal Continued -->
          <template v-else-if="i - 1 === 2">
            <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
              <div class="ch">CHAPTER 02 — ARSENAL CONTINUED</div>
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
              <div class="ch">CHAPTER 03 — TRAINING ARC</div>
              <div style="flex:1;padding:0.45rem;display:flex;flex-direction:column;gap:3px;overflow:hidden;">
                <div style="border:2px solid #0a0a0a;padding:0.52rem;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UNITEC</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Mechatronics Engineering</div><div style="font-size:0.56rem;color:#888;font-weight:700;">2012–2017</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UNITEC</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Master's in Finance</div><div style="font-size:0.56rem;color:#888;font-weight:700;">2018–2021</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">CODECADEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">Full-Stack Engineer Career Path</div></div>
                <div style="border:2px solid #0a0a0a;padding:0.52rem;"><div style="font-family:'Bangers',cursive;font-size:0.88rem;letter-spacing:2px;">UDEMY</div><div style="font-size:0.68rem;font-weight:700;color:#333;">C# Language Course</div></div>
                <div style="border:2px solid #0a0a0a;background:#0a0a0a;padding:0.65rem;flex:1;display:flex;flex-direction:column;justify-content:center;position:relative;overflow:hidden;">
                  <div style="font-family:'Bangers',cursive;font-size:2.5rem;color:#fff;opacity:0.05;position:absolute;right:-5px;bottom:-5px;line-height:0.85;">RARE BUILD</div>
                  <div style="font-family:'Bangers',cursive;font-size:0.56rem;letter-spacing:3px;color:#444;position:relative;z-index:1;">PROTAGONIST CLASS</div>
                  <div style="font-family:'Bangers',cursive;font-size:0.88rem;color:#fff;letter-spacing:2px;line-height:1.4;margin-top:0.2rem;position:relative;z-index:1;">ENGINEER → FINANCE<br>→ FULL STACK DEV</div>
                  <div style="font-size:0.58rem;color:#444;font-style:italic;margin-top:0.3rem;position:relative;z-index:1;">A rare multi-class character.</div>
                </div>
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

  <!-- Navigation -->
  <div class="nav-arrows">
    <button class="arrow-btn" :disabled="isPrevDisabled" @click="prevPage">◀ PREV</button>
    <span class="page-indicator">{{ indicatorText }}</span>
    <button class="arrow-btn" :disabled="isNextDisabled" @click="nextPage">NEXT ▶</button>
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
}

.book {
  position: relative;
  width: 300px;
  height: 520px;
  transform-style: preserve-3d;
  transform: rotateY(-20deg) rotateX(5deg);
  transition: transform 0.6s ease;
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
</style>
