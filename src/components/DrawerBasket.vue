<template>
  <div
    @click="() => emit('closeDrawer')"
    class="fixed top-0 left-0 w-full h-full bg-black z-1 opacity-55"
  ></div>

  <div
    class="fixed top-0 right-0 w-96 h-full bg-white z-1 p-8 overflow-auto flex flex-col justify-between"
  >
    <div :class="totalPrice ? '' : 'h-full'">
      <div class="flex items-center gap-3">
        <svg
          @click="() => emit('closeDrawer')"
          class="relative top-0.5 opacity-60 cursor-pointer w-4.5 h-4.5 rotate-180 hover:opacity-100 transition"
          width="16"
          height="14"
          viewBox="0 0 16 14"
          fill="none"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M1 7H14.7143"
            stroke="black"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
          <path
            d="M8.71436 1L14.7144 7L8.71436 13"
            stroke="black"
            stroke-width="2"
            stroke-linecap="round"
            stroke-linejoin="round"
          />
        </svg>
        <h2 class="text-2xl font-bold">Корзина</h2>
      </div>

      <div v-auto-animate ref="listContainer" :class="!totalPrice ? 'flex h-full items-center' : ''">
        <info-block
          v-if="!totalPrice"
          title="Корзина пустая"
          description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
          image-url="/package-icon.png"
        />
        <cart-item-list v-else />
      </div>
    </div>

    <div class="flex flex-col gap-4" v-if="totalPrice">
      <div class="flex gap-1">
        <span>Итого:</span>
        <div class="border-b border-dashed flex-1"></div>
        <b>{{ props.totalPrice }} руб.</b>
      </div>
      <div class="flex gap-1">
        <span>Налог 5%:</span>
        <div class="border-b border-dashed flex-1"></div>
        <b>{{ props.vatPrice }} руб.</b>
      </div>
      <button
        @click="() => emit('createOrder')"
        :disabled="!props.totalPrice"
        class="bg-lime-500 rounded-2xl mt-3 w-full py-2 text-white hover:bg-lime-700 transition cursor-pointer disabled:bg-slate-400 disabled:cursor-default"
      >
        {{ props.isCreatingOrder ? 'Заказ оформляется...' : 'Оформить заказ' }}
      </button>
    </div>
  </div>
</template>


<script setup>
import { ref } from 'vue'

import CartItemList from './CartItemList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number,
  isCreatingOrder: Boolean,
})
const emit = defineEmits(['closeDrawer', 'createOrder'])

const listContainer = ref(null)
</script>

