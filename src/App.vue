<script setup lang="ts">
import { ref, computed, watch, onMounted } from 'vue'
import type { Ref } from 'vue'
import Card from './components/Card.vue'
import type ShoppingItem from './ShoppingItem'
import './style.css'

const shoppingItem: Ref<ShoppingItem> = ref({} as ShoppingItem)
const shoppingItems: Ref<Array<ShoppingItem>> = ref([] as Array<ShoppingItem>)
const itemInput: Ref<HTMLInputElement | null> = ref(null)
const showForm: Ref<boolean> = ref(true) // State variable for toggling form visibility

onMounted(() => {
  // Load shoppingItems from local storage
  loadFromLocalStorage()
  if (itemInput.value) {
    itemInput.value.focus()
  }
})

// Function to load shoppingItems from local storage
function loadFromLocalStorage(): void {
  const data = localStorage.getItem('shoppingItems')
  if (data) {
    shoppingItems.value = JSON.parse(data)
  }
}

// Function to save shoppingItems to local storage
function saveToLocalStorage(): void {
  if (shoppingItems.value.length > 0) {
    localStorage.setItem('shoppingItems', JSON.stringify(shoppingItems.value))
  } else {
    localStorage.removeItem('shoppingItems')
  }
}

// Update saveToLocalStorage whenever shoppingItems changes
watch(shoppingItems, saveToLocalStorage, { deep: true })

function onSubmit(): void {
  shoppingItems.value.push(shoppingItem.value)
  shoppingItem.value = {} as ShoppingItem
  if (itemInput.value) {
    itemInput.value.focus()
  }
}

function deleteItem(index: number): void {
  shoppingItems.value.splice(index, 1)
}

function modifyItem(index: number): void {
  const itemToModify = shoppingItems.value[index]
  if (shoppingItem.value.item) {
    return
  }
  shoppingItem.value = { ...itemToModify }
  shoppingItems.value.splice(index, 1)
  showForm.value = true
}

function toggleForm(): void {
  showForm.value = !showForm.value
}

// a computed ref - Total price
const totalPrice = computed(() => {
  return shoppingItems.value.reduce(
    (accumulator, current) => accumulator + current.quantity * current.unitPrice,
    0
  )
})
</script>

<template>
  <button @click="toggleForm" class="toggle-button">{{ showForm ? 'Hide' : 'Show' }} Form</button>
  <form v-if="showForm" @submit.prevent="onSubmit">
    <label for="item">Item</label>
    <input type="text" id="item" v-model="shoppingItem.item" ref="itemInput" required />

    <br />
    <label for="unit-price">Unit price</label>
    <input type="number" id="unit-price" step="0.01" v-model="shoppingItem.unitPrice" required />
    <br />
    <label for="quantity">Quantity</label>
    <input type="number" id="quantity" step="0.01" v-model="shoppingItem.quantity" required />

    <br />
    <button type="submit">Add item</button>
  </form>
  <div v-show="shoppingItems.length > 0" class="cards-container">
    <Card
      v-for="(item, index) in shoppingItems"
      :key="index"
      :item="item"
      @modify="modifyItem(index)"
      @delete="deleteItem(index)"
    />
    <!-- <div v-for="(item, index) in shoppingItems" :key="index" class="card">
      <div class="card-content">
        <p><strong>Item:</strong> {{ item.item }}</p>
        <p><strong>Unit Price:</strong> {{ item.unitPrice }}</p>
        <p><strong>Quantity:</strong> {{ item.quantity }}</p>
        <p>
          <strong>Price:</strong>
          {{ Number((item.unitPrice * item.quantity).toFixed(2)).toLocaleString('de-DE') }}
        </p>
        <div class="card-actions">
          <button @click="modifyItem(index)">✏️</button>
          <button @click="deleteItem(index)">🗑️</button>
        </div>
      </div>
    </div> -->
  </div>
  <p class="total-price">
    Total price: {{ Number(totalPrice.toFixed(2)).toLocaleString('de-DE') }}
  </p>
</template>

<style scoped></style>
