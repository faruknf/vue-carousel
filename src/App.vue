<template>
  <vue-carousel direction="vertical">
    <img
      style="object-fit: cover"
      v-for="n in 3"
      :key="n"
      :src="'img' + n + '.jpg'"
      width="300"
      height="500"
      alt=""
      srcset=""
    />
  </vue-carousel>
</template>

<script setup>
import VueCarousel from "./components/VueCarousel.vue";
import "./assets/base.css";
</script>

