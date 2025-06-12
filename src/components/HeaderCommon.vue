<template>
    <header class="flex justify-between border-b border-slate-300 p-8 ">
      <div class="flex items-center gap-4">
        <img src="/logo.png" alt="Логотип магазина" class="w-10">
        <div>
          <h2 class="text-xl font-bold uppercase">FlexStep</h2>
          <p class="text-slate-500">Магазин ваших кроссовок</p>
        </div>
      </div>

      <ul class="flex items-center gap-6">
        <li class="flex items-center gap-2 text-gray-500 hover:text-black cursor-pointer">
          <img src="/cart.svg" alt="Иконка корзины" class="w-5">
          <b>{{ props.basketCost }} руб.</b>
        </li>
        <li class="flex items-center gap-2 text-gray-500 hover:text-black cursor-pointer">
          <img src="/heart.svg" alt="Закладки" class="w-5 h-5">
          <span>Закладки</span>
        </li>
        <li class="flex items-center gap-2 text-gray-500 hover:text-black cursor-pointer">
          <img src="/profile.svg" alt="Профиль" >
          <span>Профиль</span>
        </li>
      </ul>
    </header>
</template>

<script setup>

  const props = defineProps({
    basketCost: {
      type: Number,
    }
  })

</script>
