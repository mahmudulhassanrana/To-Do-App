<script setup>
import { ref } from 'vue'
import { router } from '@inertiajs/vue3'
import axios from 'axios'

const props = defineProps({ tasks: Array })

// Reactive form data
const form = ref({
  id: null,
  title: '',
  body: '',
  status: 'pending',
  priority: 'medium',
  due_date: null,
})

// Submit task (create or update)
const submit = async () => {
  try {
    await axios.get('/sanctum/csrf-cookie')

    if (form.value.id) {
      await axios.post(`/tasks/${form.value.id}`, form.value)
    } else {
      await axios.post('/tasks', form.value)
    }

    closeModalAndReset()
    router.reload()
  } catch (error) {
    console.error('Failed to submit task:', error)
  }
}

// Edit task → populate form & show modal
const editTask = (task) => {
  form.value = {
    id: task.id,
    title: task.title,
    body: task.body,
    status: task.status,
    priority: task.priority,
    due_date: task.due_date,
  }
  const modal = new bootstrap.Modal(document.getElementById('addTaskModal'))
  modal.show()
}

// Delete task
const deleteTask = async (id) => {
  if (confirm('Are you sure you want to delete this task?')) {
    try {
      await axios.get('/sanctum/csrf-cookie')
      await axios.delete(`/tasks/${id}`)
      router.reload()
    } catch (error) {
      console.error('Delete failed:', error)
    }
  }
}

// Reset form and close modal
const closeModalAndReset = () => {
  const modal = bootstrap.Modal.getInstance(document.getElementById('addTaskModal'))
  if (modal) modal.hide()
  form.value = {
    id: null,
    title: '',
    body: '',
    status: 'pending',
    priority: 'medium',
    due_date: null,
  }
}

// Logout
const logout = async () => {
  try {
    await axios.post('/logout')
    router.visit('/login')
  } catch (error) {
    console.error('Logout failed:', error)
  }
}
</script>


<template>
    <div class="container py-4">
      <!-- Header -->
      <div class="d-flex justify-content-between align-items-center mb-4">
        <h2 class="mb-0">My To-Do List</h2>
        <button class="btn btn-outline-danger" @click="logout">Logout</button>
      </div>

      <!-- Add Task Button -->
      <div class="text-end mb-3">
        <button class="btn btn-success" data-bs-toggle="modal" data-bs-target="#addTaskModal">
          + Add Task
        </button>
      </div>

      <!-- Task List -->
      <div v-if="tasks.length" class="list-group">
        <div v-for="task in tasks" :key="task.id" class="list-group-item d-flex justify-content-between align-items-center">
          <div>
            <strong>{{ task.title }}</strong><br />
            <small>{{ task.body }}</small><br />
            <small>Status: {{ task.status }} | Priority: {{ task.priority }} | Due: {{ task.due_date }}</small>
          </div>
          <div class="btn-group">
            <button class="btn btn-sm btn-primary" @click="editTask(task)">Edit</button>
            <button class="btn btn-sm btn-danger" @click="deleteTask(task.id)">Delete</button>
          </div>
        </div>
      </div>
      <p v-else class="text-muted">No tasks found.</p>

      <!-- Add/Edit Task Modal -->
      <div class="modal fade" id="addTaskModal" tabindex="-1" aria-labelledby="addTaskModalLabel" aria-hidden="true">
        <div class="modal-dialog">
          <form @submit.prevent="submit" class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="addTaskModalLabel">{{ form.id ? 'Edit Task' : 'Add Task' }}</h5>
              <button type="button" @click="closeModalAndReset" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
              <div class="mb-3">
                <label class="form-label">Title</label>
                <input v-model="form.title" class="form-control" required />
              </div>
              <div class="mb-3">
                <label class="form-label">Description</label>
                <textarea v-model="form.body" class="form-control" required></textarea>
              </div>
              <div class="row">
                <div class="col-md-4 mb-3">
                  <label class="form-label">Status</label>
                  <select v-model="form.status" class="form-select">
                    <option value="pending">Pending</option>
                    <option value="in_progress">In Progress</option>
                    <option value="completed">Completed</option>
                  </select>
                </div>
                <div class="col-md-4 mb-3">
                  <label class="form-label">Priority</label>
                  <select v-model="form.priority" class="form-select">
                    <option value="low">Low</option>
                    <option value="medium">Medium</option>
                    <option value="high">High</option>
                  </select>
                </div>
                <div class="col-md-4 mb-3">
                  <label class="form-label">Due Date</label>
                  <input type="date" v-model="form.due_date" class="form-control" />
                </div>
              </div>
            </div>
            <div class="modal-footer">
              <button type="button" @click="closeModalAndReset" class="btn btn-secondary" data-bs-dismiss="modal">Cancel</button>
              <button type="submit" class="btn btn-primary">
                {{ form.id ? 'Update Task' : 'Add Task' }}
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </template>

