<template>
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

<script setup lang="ts">
import coverImg from '../../../assets/cover.png'
</script>
