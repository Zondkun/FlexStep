<template>
  <h2 class="text-4xl mb-10">Избранное</h2>
  <div class="mt-10">
    <card-list-render @add-to-drawer-carts="onClickAddPlus" :items="favorites" @add-to-favorite="addToFavorite" />
  </div>
</template>

<script setup>
import { ref, onMounted, inject } from 'vue'
import CardListRender from '@/components/CardListRender.vue'

const favorites = ref([])
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
const {addToDrawerCarts, removeFromDrawerCarts} = inject('drawerCarts')
const { drawerCarts } = inject('drawerCarts')
onMounted(() => {
  fetch("https://afa71d2f9b6cbbe6.mokky.dev/favorites?_relations=items")
  .then(res => res.json())
  .then(data => {
    favorites.value = data.map((obj) => {
      const item = obj.item
      return {
        ...item,
        isFavorite: true,
        favoriteId: obj.id,
        isAdded: drawerCarts.value.some(cartItem => cartItem.id === item.id),
      }
    })
  })
  .catch(err => {
    console.log(err.message)
  })
})
</script>
