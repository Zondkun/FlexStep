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
      <RouterView />
    </div>
  </div>
</template>

<script setup>
import { ref, watch, provide, computed } from 'vue'

import HeaderCommon from '@/components/HeaderCommon.vue'
import DrawerBasket from '@/components/DrawerBasket.vue'

const sneakers = ref([])
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
  const index = drawerCarts.value.findIndex(itemDrawer => itemDrawer.id === item.id)
  if (index !== -1) {
    drawerCarts.value.splice(index, 1)
    item.isAdded = false
  }
}

const drawerOpen = ref(false)
const openDrawer = () => {
  drawerOpen.value = true
}
const closeDrawer = () => {
  drawerOpen.value = false
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
  removeFromDrawerCarts,
  sneakers
})

watch(drawerCarts, () => {
  // Сохраняем корзину в localStorage
  localStorage.setItem('drawerCarts', JSON.stringify(drawerCarts.value))

  // Обновляем флаг isAdded у всех кроссовок
  sneakers.value = sneakers.value.map((item) => {
    return {
      ...item,
      isAdded: drawerCarts.value.some((cartItem) => cartItem.id === item.id)
    }
  })
}, { deep: true })

</script>
