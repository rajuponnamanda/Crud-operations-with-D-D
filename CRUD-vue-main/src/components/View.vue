<template>
  <div>
    <div class="row">
      <div class="col-md-4" v-for="item in items" :key="item.id">
        <div class="card mb-3">
          <div class="card-body">
            <h5 class="card-title">{{ item.name }}</h5>
            <p class="card-text">Email: {{ item.email }}</p>
            <p class="card-text">Contact: {{ item.contact }}</p>
            <p class="card-text">Age: {{ item.age }}</p>
            <button class="btn btn-danger" @click="deleteItem(item.id)">Delete</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, defineProps } from 'vue';
import axios from 'axios';

// Define props to receive emitted data

const items = ref([]);

// Fetch initial list of items
async function fetchItems() {
  try {
    const response = await axios.get('http://localhost:3000/users');
    items.value = response.data;
    console.log(items.value)
  } catch (error) {
    console.error(error);
  }
}

onMounted(fetchItems);

// Listen for the 'item-added' event emitted by the add component
// and update the list of items
onItemAdded((addedItem) => {
  items.value.push(addedItem);
});

// Function to delete an item
async function deleteItem(id) {
  try {
    await axios.delete(`http://localhost:3000/users/${id}`);
    items.value = items.value.filter(item => item.id !== id);
  } catch (error) {
    console.error(error);
  }
}
</script>

<style scoped>
.card {
  width: 18rem;
}
</style>

