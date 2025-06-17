<template>
  <div class="flex items-center border border-slate-300 rounded-xl p-4 gap-4 mt-3">
    <img class="w-16 h-16" :src="props.imageUrl" :alt="props.title" />
    <div class="flex flex-col w-full">
      <p>{{ props.title }}</p>
      <div class="flex justify-between mt-2">
        <b>{{ props.price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-60 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
          alt="Закрыть/Убрать"
        />
      </div>
    </div>
  </div>
</template>

<script setup>
const props = defineProps({
  id: Number,
  title: String,
  imageUrl: String,
  price: Number,
})

const emit = defineEmits(['onClickRemove'])
</script>
