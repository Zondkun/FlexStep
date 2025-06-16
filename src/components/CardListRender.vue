<template>
  <div class="grid grid-cols-4 gap-6">
    <sneakers-card v-for="item in props.items" :key="item.id"
      :id="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :is-favorite="item.isFavorite"
      :is-added="false"
      :on-click-favorite="() => emit('addToFavorite', item)"
    >
    </sneakers-card>
  </div>
</template>

<script setup>
import SneakersCard from '@/components/SneakersCard.vue'

const props = defineProps({
  items: Array,
})

const emit = defineEmits(["addToFavorite"]);
</script>
