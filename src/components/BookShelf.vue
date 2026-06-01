<template>
<div class="shelf-scene" :class="{ 'shelf-leaving': isLeaving }">

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
</template>

<style scoped>
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

.bookshelf-wrap { perspective: 1100px; }
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
.shelf-section.shelf-mid { min-height: 148px; }
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
  box-shadow: 0 0 18px rgba(200,148,42,0.35), inset -3px 0 6px rgba(0,0,0,0.4), 3px 3px 10px rgba(0,0,0,0.6);
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
  background: linear-gradient(105deg, transparent 30%, rgba(255,255,255,0.45) 50%, transparent 70%);
  transform: translateX(-150%);
  pointer-events: none;
}
.manga-spine.spine-hover {
  transform: translateY(-9px);
  box-shadow: 0 12px 24px rgba(0,0,0,0.6), 0 0 16px rgba(200,148,42,0.4);
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

.pop-enter-active { animation: pop-in 0.25s cubic-bezier(0.34,1.56,0.64,1); }
.pop-leave-active { animation: pop-in 0.18s reverse ease-in; }
@keyframes pop-in {
  from { opacity: 0; transform: scale(0.82); }
  to   { opacity: 1; transform: scale(1); }
}
</style>

<script setup lang="ts">
import { ref } from 'vue'
import type { CSSProperties } from 'vue'

defineProps<{ caseStyle: CSSProperties }>()
const emit = defineEmits<{ confirm: [] }>()

const showConfirm = ref(false)
const isLeaving = ref(false)
const isSpineHovered = ref(false)

function onBookClick() { showConfirm.value = true }
function onConfirmNo() { showConfirm.value = false }
function onConfirmYes() {
  showConfirm.value = false
  isLeaving.value = true
  setTimeout(() => emit('confirm'), 900)
}
function onSpineEnter() { isSpineHovered.value = true }
function onSpineLeave() { isSpineHovered.value = false }
</script>
