<script setup lang="ts">
import type ShoppingItem from '../ShoppingItem'
import '../style.css'
// Export the component as default

const props = defineProps<{ item: ShoppingItem }>()

// Define emits
const emit = defineEmits(['modify', 'delete'])

// Define methods to emit events
function handleModify() {
  emit('modify')
}

function handleDelete() {
  emit('delete')
}
</script>

<template>
  <div class="card">
    <div class="card-content">
      <p><strong>Item:</strong> {{ item.item }}</p>
      <p><strong>Unit Price:</strong> {{ item.unitPrice }}</p>
      <p><strong>Quantity:</strong> {{ item.quantity }}</p>
      <p>
        <strong>Price:</strong>
        {{ Number((item.unitPrice * item.quantity).toFixed(2)).toLocaleString('de-DE') }}
      </p>
      <div class="card-actions">
        <button @click="handleModify">✏️</button>
        <button @click="handleDelete">🗑️</button>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
