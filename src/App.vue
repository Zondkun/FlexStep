<template>
  <drawer-basket
    @create-order="createOrder"
    :total-price="totalPrice"
    :vat-price="vatPrice"
    @close-drawer="closeDrawer"
    :is-creating-order="isCreatingOrders"
    v-if="drawerOpen" />
  <div class="w-4/5 mx-auto bg-white rounded-xl shadow-xl mt-15">
    <header-common :total-price="totalPrice" @open-drawer="openDrawer" ></header-common>

    <div class="p-10">
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
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, reactive, provide, computed } from 'vue'

import HeaderCommon from '@/components/HeaderCommon.vue'
import CardListRender from '@/components/CardListRender.vue'
import DrawerBasket from '@/components/DrawerBasket.vue'

const sneakers = ref([])
const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
})
const isCreatingOrders = ref(false);
const drawerCarts = ref([])
const totalPrice = computed(() => {
  return drawerCarts.value.reduce((acc, item) => acc + item.price, 0)
})

const vatPrice = computed(() => {
  return Math.round((totalPrice.value*5)/100)
})

const addToDrawerCarts = (item) => {
  drawerCarts.value.push(item)
  item.isAdded = true
}

const removeFromDrawerCarts = (item) => {
  drawerCarts.value.splice(drawerCarts.value.indexOf(item), 1)
  item.isAdded = false
}

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToDrawerCarts(item)
  } else {
    removeFromDrawerCarts(item)
  }
}

const drawerOpen = ref(false)
const openDrawer = () => {
  drawerOpen.value = true
}
const closeDrawer = () => {
  drawerOpen.value = false
}

const onChangeValue = (event) => {
  filters.sortBy = event.target.value
}

const onChangeInputValue = (event) => {
  filters.searchQuery = event.target.value
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

function fetchFavorites() {
  fetch(`https://afa71d2f9b6cbbe6.mokky.dev/favorites`)
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка: ${response.status}`)
      }
      return response.json()
    })
    .then((data) => {
      sneakers.value = sneakers.value.map((item) => {
        const favorite = data.find((fav) => fav.parentId === item.id)
        if (!favorite) {
          return item
        } else {
          return {
            ...item,
            isFavorite: true,
            favoriteId: favorite.id,
          }
        }
      })
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

function loadSneakers() {
  fetch(
    `https://afa71d2f9b6cbbe6.mokky.dev/items?limit=12&sortBy=${filters.sortBy}&title=*${filters.searchQuery}*`,
  )
    .then((response) => {
      if (!response.ok) {
        throw new Error(`Ошибка: ${response.status}`)
      }
      return response.json()
    })
    .then((data) => {
      sneakers.value = data.items.map((obj) => {
        return {
          ...obj,
          isAdded: false,
          isFavorite: false,
          favoriteId: null,
        }
      })
    })
    .catch((error) => {
      console.error('Ошибка при получении данных:', error)
    })
}

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
        parentId: item.id,
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
        console.log('Добавлено в избранное:', data)
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
        console.log('Удалено из избранного')
      })
      .catch((error) => {
        console.error('Ошибка при удалении из избранного:', error)
      })
      .finally(() => {
        item.isLoading = false
      })
  }
}

function createOrder() {
  isCreatingOrders.value = true
  fetch(`https://afa71d2f9b6cbbe6.mokky.dev/orders`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      items: drawerCarts.value,
      totalPrice: totalPrice.value,
    }),
  })
  .then((response) => {
    if (!response.ok) {
      throw new Error(`Заказ не получилось оформить: ${response.status}`)
    }
    console.log('Заказ оформлен')
    sneakers.value.map((item) => {
      item.isAdded = false
    })
    drawerCarts.value = []
    return response.json() // Дописать then(data)
  })
  .catch((error) => {
    console.error(error.message)
  })
  .finally(() => {
    isCreatingOrders.value = false;
  })
}

provide('drawerCarts', {
  drawerCarts,
  addToDrawerCarts,
  removeFromDrawerCarts
}) // Избавляемся от props drilling

onMounted(() => {
  loadSneakers()
  fetchFavorites()
})

const debounceLoad = debounced(loadSneakers, 500)
watch(() => filters.sortBy, loadSneakers) // попадает newVal, oldVal
watch(() => filters.searchQuery, debounceLoad) // попадает newVal, oldVal -> передается как ...args в loadSneakers
</script>
