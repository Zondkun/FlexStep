<template>
  <div class="w-4/5 mx-auto bg-white h-screen rounded-xl shadow-xl mt-15">
    <header-common :basket-cost="basketCost"></header-common>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import HeaderCommon from '@/components/HeaderCommon.vue'

let basketCost = ref(1205);

</script>

