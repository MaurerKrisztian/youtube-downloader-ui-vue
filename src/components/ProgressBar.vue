<script setup lang="ts">
const props = defineProps(['percent'])
</script>

<template>
  <div class="wrap-circles">
    <div class="circle" :style=" {'background-image': 'conic-gradient(#B5838D '+props.percent+'%, #FFCDB2 0)' }">
      <div class="inner"> {{props.percent}}%</div>
    </div>
  </div>
</template>

<style>

body {
  font-family: "Lato";
  font-size: 16px;
}

.circle {
  position: relative;
  width: 150px;
  height: 150px;
  margin: 0.5rem;
  border-radius: 50%;
  background: #FFCDB2;
  overflow: hidden;
}
.circle .inner {
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 115px;
  height: 115px;
  background: #000;
  border-radius: 50%;
  font-size: 1.85em;
  font-weight: 300;
  color: rgba(255, 255, 255, 0.75);
}
</style>
