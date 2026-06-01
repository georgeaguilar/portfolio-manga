<template>
  <div style="display:flex;flex-direction:column;height:100%;background:#fff;">
    <div class="ch">&nbsp;</div>
    <div style="flex:1;overflow:hidden;background:#000;position:relative;">
      <img :src="originArcImg" style="width:100%;height:100%;object-fit:cover;" alt="Origin Arc" />
    </div>
  </div>
</template>

<script setup lang="ts">
import originArcImg from '../../../assets/origin-arc.png'
</script>
