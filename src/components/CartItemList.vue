<template>
  <div class="flex flex-col gap-2 my-4" v-auto-animate>
    <cart-item
      v-for="item in drawerCarts" :key="item.id"
      :title="item.title"
      :price="item.price"
      :image-url="item.imageUrl"
      @on-click-remove="() => removeFromDrawerCarts(item)"
    />
  </div>
</template>

<script setup>
import CartItem from '@/components/CartItem.vue'
import { inject } from 'vue'

const { drawerCarts, removeFromDrawerCarts } = inject('drawerCarts')
</script>
