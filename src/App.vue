<template>
<!--  <drawer-basket/>-->
  <div class="w-4/5 mx-auto bg-white rounded-xl shadow-xl mt-15">
    <header-common :basket-cost="basketCost"></header-common>

    <div class="p-10">
      <div class="flex justify-between items-start">
        <h2 class="text-4xl mb-10">Все кроссовки</h2>

        <div class="flex gap-4">
          <select class="border rounded-md border-gray-500 py-2 px-3 outline-none">
            <option value="">По названию</option>
            <option value="">Дешевле</option>
            <option value="">Дороже</option>
          </select>
          <div class="relative">
            <img src="/search.svg" alt="Поиск" class="absolute top-3 left-3">
            <input class="border border-gray-500 rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-700"
                   type="text"
                   placeholder="Поиск...">

          </div>
        </div>
      </div>
      <card-list-render :items="items"/>

    </div>

  </div>
</template>

<script setup>
import { ref } from 'vue';
import HeaderCommon from '@/components/HeaderCommon.vue'
import CardListRender from '@/components/CardListRender.vue'
import DrawerBasket from '@/components/DrawerBasket.vue'

let basketCost = ref(1205);
const items = ref([
  {
    "id": 1,
    "title": "Мужские Кроссовки Nike Blazer Mid Suede",
    "price": 12999,
    "imageUrl": "/sneakers/sneakers-1.jpg"
  },
  {
    "id": 2,
    "title": "Мужские Кроссовки Nike Air Max 270",
    "price": 15600,
    "imageUrl": "/sneakers/sneakers-2.jpg"
  },
  {
    "id": 3,
    "title": "Мужские Кроссовки Nike Blazer Mid Suede",
    "price": 8499,
    "imageUrl": "/sneakers/sneakers-3.jpg"
  },
  {
    "id": 4,
    "title": "Кроссовки Puma X Aka Boku Future Rider",
    "price": 7800,
    "imageUrl": "/sneakers/sneakers-4.jpg"
  },
  {
    "id": 5,
    "title": "Кроссовки Future Rider",
    "price": 9550,
    "imageUrl": "/sneakers/sneakers-5.jpg"
  },
  {
    "id": 6,
    "title": "Кроссовки Black Edition",
    "price": 16999,
    "imageUrl": "/sneakers/sneakers-6.jpg"
  },
  {
    "id": 7,
    "title": "Кроссовки Orange Boomb Edition",
    "price": 7499,
    "imageUrl": "/sneakers/sneakers-7.jpg"
  },
  {
    "id": 8,
    "title": "Кроссовки Nike Air Max 270",
    "price": 15600,
    "imageUrl": "/sneakers/sneakers-8.jpg"
  },
  {
    "id": 9,
    "title": "Кроссовки Nike Air Force 1",
    "price": 5900,
    "imageUrl": "/sneakers/sneakers-9.jpg"
  },
  {
    "id": 10,
    "title": "Кроссовки Adidas Ultraboost",
    "price": 11500,
    "imageUrl": "/sneakers/sneakers-10.jpg"
  },
  {
    "id": 11,
    "title": "Кроссовки Puma Clyde All-Pro",
    "price": 7600,
    "imageUrl": "/sneakers/sneakers-11.jpg"
  },
  {
    "id": 12,
    "title": "Кроссовки Converse Chuck Taylor All-Star",
    "price": 13000,
    "imageUrl": "/sneakers/sneakers-12.jpg"
  },
]); // массив данных о кроссовках

</script>

