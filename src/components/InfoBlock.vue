<template>
  <div class="flex flex-col items-center w-72 mx-auto">
    <img class="w-30 h-30" :src="props.imageUrl" :alt="props.title">
    <h2 class="mt-4 text-2xl font-medium">{{props.title}}</h2>
    <p class="text-center text-gray-500 mt-2">{{props.description}}</p>
  </div>
</template>

<script setup>

const props = defineProps({
  title: String,
  imageUrl: String,
  description: String,
})

</script>
