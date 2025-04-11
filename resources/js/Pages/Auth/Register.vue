<script setup>
import { useForm, router } from '@inertiajs/vue3';
import axios from 'axios';

const form = useForm({
  name: '',
  email: '',
  password: '',
  password_confirmation: '',
})

const submit = async () => {
  await axios.get('/sanctum/csrf-cookie')
  await axios.post('/register', form)
  router.get('/tasks')
}
</script>

<template>
    <div class="max-w-md mx-auto mt-10">
      <h1 class="text-2xl font-bold mb-4">Register</h1>
      <form @submit.prevent="submit">
        <div class="mb-4">
          <input
            v-model="form.name"
            type="text"
            placeholder="Name"
            class="w-full border rounded p-2"
            required
          />
        </div>
        <div class="mb-4">
          <input
            v-model="form.email"
            type="email"
            placeholder="Email"
            class="w-full border rounded p-2"
            required
          />
        </div>
        <div class="mb-4">
          <input
            v-model="form.password"
            type="password"
            placeholder="Password"
            class="w-full border rounded p-2"
            required
          />
        </div>
        <div class="mb-4">
          <input
            v-model="form.password_confirmation"
            type="password"
            placeholder="Confirm Password"
            class="w-full border rounded p-2"
            required
          />
        </div>
        <button
          type="submit"
          class="w-full bg-green-600 text-white py-2 rounded hover:bg-green-700"
        >
          Register
        </button>
      </form>
    </div>
</template>

