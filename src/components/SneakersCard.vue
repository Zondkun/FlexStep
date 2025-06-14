<template>
  <div
    class="relative border border-slate-200 rounded-2xl p-8 cursor-pointer transition duration-300 hover:shadow-xl"
  >
    <img @click="onClickFavorite" :src="!isFavorite ? '/like-1.svg' : '/like-2.svg' " alt="лайкнуто" class="absolute top-5 left-5 w-10">
    <img :src="props.imageUrl" alt="Зеленые кроссовки мужские" class="" />
    <p class="mt-1">{{ props.title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-700">Цена:</span>
        <b>{{ props.price }} руб.</b>
      </div>
      <img @click="onClickAdd" :src="!isAdded ? '/plus.svg' : '/checked.svg' " alt="Добавить в корзину" />
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  imageUrl: String,
  title: String,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickAdd: Function,
  onClickFavorite: Function,
})
</script>
