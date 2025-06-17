<template>
  <div class="flex justify-between items-start">
    <h2 class="text-4xl mb-10">Все кроссовки</h2>

    <div class="flex gap-4">
      <select
        @change="onChangeValue"
        class="border rounded-md border-gray-400 focus:border-gray-500 py-2 px-3 outline-none"
      >
        <option value="title">По названию</option>
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
    <card-list-render @add-to-drawer-carts="onClickAddPlus" :items="sneakers" @add-to-favorite="addToFavorite" />
  </div>
</template>

<script setup>
import CardListRender from '@/components/CardListRender.vue'
import { inject, onMounted, reactive, watch } from 'vue'

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})
function addToFavorite(item) {
  if (item.isLoading) return // блокировка частых запросов
  item.isLoading = true

  if (!item.isFavorite) {
    item.isFavorite = true
    fetch('https://afa71d2f9b6cbbe6.mokky.dev/favorites', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        item_id: item.id,
      }),
    })
      .then((response) => {
        if (!response.ok) {
          throw new Error(`Ошибка: ${response.status}`)
        }
        return response.json()
      })
      .then((data) => {
        item.favoriteId = data.id
      })
      .catch((error) => {
        console.error('Ошибка при добавлении в избранное:', error)
      })
      .finally(() => {
        item.isLoading = false
      })
  } else {
    item.isFavorite = false
    fetch(`https://afa71d2f9b6cbbe6.mokky.dev/favorites/${item.favoriteId}`, {
      method: 'DELETE',
    })
      .then((response) => {
        if (!response.ok) {
          throw new Error(`Ошибка: ${response.status}`)
        }
        item.favoriteId = null
      })
      .catch((error) => {
        console.error('Ошибка при удалении из избранного:', error)
      })
      .finally(() => {
        item.isLoading = false
      })
  }
}
const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToDrawerCarts(item)
  } else {
    removeFromDrawerCarts(item)
  }
}
const onChangeValue = (event) => {
  filters.sortBy = event.target.value
}
function loadSneakers() {
  Promise.all([
    fetch(`https://afa71d2f9b6cbbe6.mokky.dev/items?limit=12&sortBy=${filters.sortBy}&title=*${filters.searchQuery}*`)
      .then(res => res.json()),
    fetch(`https://afa71d2f9b6cbbe6.mokky.dev/favorites`)
      .then(res => res.json()),
  ])
    .then(([itemsData, favoritesData]) => {
      sneakers.value = itemsData.items.map((obj) => {
        const isAdded = drawerCarts.value.some(cartItem => cartItem.id === obj.id)
        const favorite = favoritesData.find(fav => fav.item_id === obj.id)

        return {
          ...obj,
          isAdded,
          isFavorite: !!favorite,
          favoriteId: favorite ? favorite.id : null,
        }
      })
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

function debounced(fn, delay) {
  // аргументы вызова обёрнутой функции; передаются в fn, в loadSneakers не используются
  let timeoutId

  return function (...args) {
    // Могут не передаваться, но тогда не использовать в fn
    clearTimeout(timeoutId)
    timeoutId = setTimeout(() => fn(...args), delay)
  }
}

const onChangeInputValue = (event) => {
  filters.searchQuery = event.target.value
}
const debounceLoad = debounced(loadSneakers, 500)
const { sneakers, drawerCarts, addToDrawerCarts, removeFromDrawerCarts} = inject('drawerCarts')
watch(() => filters.sortBy, loadSneakers) // попадает newVal, oldVal
watch(() => filters.searchQuery, debounceLoad) // попадает newVal, oldVal -> передается как ...args в loadSneakers
onMounted(() => {
  const localDrawerCarts = localStorage.getItem('drawerCarts');
  drawerCarts.value = localDrawerCarts ? JSON.parse(localDrawerCarts) : []
  loadSneakers()
})
</script>
