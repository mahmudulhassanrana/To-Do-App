<script setup>
import { useForm, router } from '@inertiajs/vue3'
import axios from 'axios'

const form = useForm({
  email: '',
  password: '',
})

const submit = async () => {
  await axios.get('/sanctum/csrf-cookie') // Required for Sanctum
  await axios.post('/login', form)
  router.get('/tasks'); // Redirect to tasks page
}
</script>

<template>
    <div class="max-w-md mx-auto mt-10">
      <h1 class="text-2xl font-bold mb-4">Login</h1>
      <form @submit.prevent="submit">
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
        <button
          type="submit"
          class="w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700"
        >
          Login
        </button>
      </form>
    </div>
</template>

