<script setup lang="ts">
import { ref, computed } from 'vue'
import type { Ref } from 'vue'

export interface ShoppingItem {
  item: string
  unitPrice: number
  quantity: number
}

const shoppingItem: Ref<ShoppingItem> = ref({} as ShoppingItem)
const shoppingItems: Ref<Array<ShoppingItem>> = ref([] as Array<ShoppingItem>)
const itemInput: Ref<HTMLInputElement | null> = ref(null)
const showForm: Ref<boolean> = ref(true) // State variable for toggling form visibility

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
    <div v-for="(item, index) in shoppingItems" :key="index" class="card">
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
    </div>
  </div>
  <p class="total-price">
    Total price: {{ Number(totalPrice.toFixed(2)).toLocaleString('de-DE') }}
  </p>
</template>

<style scoped>
/* Basic Reset */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: Arial, sans-serif;
}

/* Styling the form */
form {
  background-color: #e0f7e9;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.6);
  max-width: 400px;
  margin: 20px auto;
}

form label {
  display: block;
  margin-bottom: 8px;
  font-weight: bold;
}

form input {
  width: 100%;
  padding: 10px;
  margin-bottom: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

form button {
  background-color: #00796b; /* Vibrant teal color */
  color: white;
  padding: 12px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
  font-size: 1em;
  transition: background-color 0.3s ease;
}

form button:hover {
  background-color: #004d40; /* Darker shade for hover effect */
}

/* Toggle button */
.toggle-button {
  display: block;
  margin: 20px auto;
  background-color: #00796b;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-weight: bold;
  font-size: 1em;
  transition: background-color 0.3s ease;
}

/* Card Layout */
.cards-container {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
  padding: 20px;
  justify-content: center;
}

.card {
  background-color: #e8f5e9;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2);
  padding: 16px;
  width: 280px;
}

.card-content {
  display: flex;
  flex-direction: column;
}

.card-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 12px;
}

.card-actions button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 1.2em;
}

.total-price {
  text-align: center;
  font-size: 1.2em;
  margin: 20px;
}

/* Responsive design */
@media (max-width: 600px) {
  form {
    width: 90%;
    margin: 10px auto;
  }

  .cards-container {
    flex-direction: column;
    align-items: center;
  }

  .card {
    width: 100%;
    max-width: 400px;
  }

  form label,
  form input,
  form button {
    font-size: 0.9em;
  }

  .total-price {
    font-size: 1em;
  }
}
</style>
