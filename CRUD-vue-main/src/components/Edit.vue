<template>
  <div class="pb-4 text-center">
    <div class="bg-primary p-2">
      <h5>Edit Details</h5>
    </div>
    <div>
      <div>
        <label for="name" class="font-medium fw-bold">Name :-</label>
        <input
          type="text"
          id="stuname"
          v-model="formData.name"
          placeholder="Write your name"
          required
          class="border border-secondary py-2 my-3 mx-4 w-50"
        />
      </div>
      <div>
        <label for="email" class="font-medium fw-bold"> Email :- </label>
        <input
          type="text"
          id="stuemail"
          v-model="formData.email"
          placeholder="Write your Email"
          required
          class="border border-secondary py-2 my-3 mx-4 w-50"
        />
      </div>
      <div>
        <label for="contact" class="font-medium fw-bold">Phone :-</label>
        <input
          type="number"
          placeholder="Enter Your Contact Number"
          v-model.trim="formData.contact"
          max="10"
          required
          class="border border-secondary py-2 my-3 mx-4 w-50"
        />
      </div>
      <div>
        <label for="age" class="font-medium fw-bold">Age :-</label>
        <input
          type="number"
          placeholder="Enter Your age"
          v-model.trim="formData.age"
          required
          class="border border-secondary py-2 my-3 mx-4 w-50"
        />
      </div>
    </div>
    <div>
      <router-link to="/">
        <button type="submit" class="btn btn-success text-dark px-5 py-2 mx-2 my-3" @click="submitForm">
          Update
        </button>
      </router-link>
      <router-link to="/">
        <button class="btn btn-secondary py-2 px-6 mr-6">Back To Home</button>
      </router-link>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, onMounted } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const formData = reactive({
  name: '',
  email: '',
  contact: '',
  age: ''
})

const router = useRouter()
const userId = router.currentRoute.value.params.id as number

async function getStudentData() {
  try {
    const response = await axios.get(`http://localhost:3000/users/${userId}`)
    const { name, email, contact, age } = response.data
    formData.name = name
    formData.email = email
    formData.contact = contact
    formData.age = age
  } catch (error) {
    console.error(error)
  }
}

async function submitForm() {
  try {
    await axios.put(`http://localhost:3000/users/${userId}`, formData)
    alert('Form successfully updated')
  } catch (error) {
    console.error(error)
  }
}

onMounted(getStudentData)
</script>

<style scoped>
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input {
  border-radius: 5px;
}
</style>
