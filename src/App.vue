<template>
  <!--  <drawer-basket/>-->
  <div class="w-4/5 mx-auto bg-white rounded-xl shadow-xl mt-15">
    <header-common :basket-cost="basketCost"></header-common>

    <div class="p-10">
      <div class="flex justify-between items-start">
        <h2 class="text-4xl mb-10">Все кроссовки</h2>

        <div class="flex gap-4">
          <select @change="onChangeValue" class="border rounded-md border-gray-400 focus:border-gray-500 py-2 px-3 outline-none">
            <option value="name">По названию</option>
            <option value="price">Дешевле</option>
            <option value="-price">Дороже</option>
          </select>
          <div class="relative">
            <img src="/search.svg" alt="Поиск" class="absolute top-3 left-3" />
            <input
              class="border border-gray-400 rounded-md py-2 pl-10 pr-4 outline-none focus:border-gray-500"
              type="text"
              placeholder="Поиск..."
              @input="onChangeInputValue"
            />
          </div>
        </div>
      </div>
      <div class="mt-10">
        <card-list-render :items="sneakers" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, reactive } from 'vue'

import HeaderCommon from '@/components/HeaderCommon.vue'
import CardListRender from '@/components/CardListRender.vue'
import DrawerBasket from '@/components/DrawerBasket.vue'

let basketCost = ref(1205)
const sneakers = ref([])
const filters = reactive({
  sortBy: 'name',
  searchQuery: '',
})

const onChangeValue = event => {
  filters.sortBy = event.target.value
}

const onChangeInputValue = event => {
  filters.searchQuery = event.target.value
}

function debounced(fn, delay) { // избыточные ...args передаются в fn (в loadSneakers не используется)
  let timeoutId;

  return function (...args) {
    clearTimeout(timeoutId)
    timeoutId = setTimeout(() => fn(...args), delay)
  }
}

function loadSneakers() {
  fetch(`https://afa71d2f9b6cbbe6.mokky.dev/items?limit=12&sortBy=${filters.sortBy}&title=*${filters.searchQuery}*`)
    .then(response => {
      if (!response.ok) {
        throw new Error(`Ошибка: ${response.status}`);
      }
      return response.json();
    })
    .then(data => {
      sneakers.value = data.items;
    })
    .catch(error => {
      console.error('Ошибка при получении данных:', error);
    });
}

onMounted(loadSneakers)

const debounceLoad = debounced(loadSneakers, 500)
watch(() => filters.sortBy, loadSneakers)// попадает newVal, oldVal
watch(() => filters.searchQuery, debounceLoad) // попадает newVal, oldVal -> передается как ...args в loadSneakers
</script>
