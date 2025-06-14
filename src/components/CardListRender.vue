<template>
  <div class="grid grid-cols-4 gap-6">
    <sneakers-card v-for="item in props.items" :key="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :is-favorite="false"
      :is-added="false"
    >
    </sneakers-card>
  </div>
</template>

<script setup>
import SneakersCard from '@/components/SneakersCard.vue'

const props = defineProps({
  items: Array,
})
</script>
