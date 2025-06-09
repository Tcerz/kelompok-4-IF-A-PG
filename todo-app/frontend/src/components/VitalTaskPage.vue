<template>
  <div class="p-6 space-y-6">
    <div class="bg-white rounded-xl shadow-sm p-6">
      <div class="flex items-center justify-between mb-6">
        <div>
          <h2 class="text-2xl font-semibold text-gray-800">Vital Tasks</h2>
          <p class="text-gray-600">High priority tasks that need immediate attention</p>
        </div>
        <div class="flex items-center space-x-4">
          <div class="flex items-center space-x-2">
            <div class="w-3 h-3 bg-red-500 rounded-full"></div>
            <span class="text-sm font-medium text-gray-700">{{ vitalTasks.length }} High Priority Tasks</span>
          </div>
          <button
            @click="showAddForm = !showAddForm"
            class="flex items-center space-x-2 px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z"/>
            </svg>
            <span>Add Vital Task</span>
          </button>
        </div>
      </div>

      <!-- Add Task Form -->
      <div v-if="showAddForm" class="mb-6 p-4 bg-red-50 border border-red-200 rounded-lg">
        <h3 class="text-lg font-semibold text-red-800 mb-4">Add New Vital Task</h3>
        <form @submit.prevent="addVitalTask" class="space-y-4">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <input
              v-model="newTask.title"
              type="text"
              placeholder="Vital task title..."
              class="px-4 py-3 border border-red-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent outline-none"
              required
            />
            <select
              v-model="newTask.status"
              class="px-4 py-3 border border-red-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent outline-none"
            >
              <option value="pending">Pending</option>
              <option value="in-progress">In Progress</option>
              <option value="completed">Completed</option>
            </select>
          </div>
          <textarea
            v-model="newTask.description"
            placeholder="Describe this vital task..."
            class="w-full px-4 py-3 border border-red-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent outline-none resize-none"
            rows="3"
          ></textarea>
          <div class="flex space-x-2">
            <button
              type="submit"
              class="bg-red-500 hover:bg-red-600 text-white font-semibold py-2 px-6 rounded-lg transition-colors"
            >
              Add Vital Task
            </button>
            <button
              type="button"
              @click="showAddForm = false"
              class="bg-gray-500 hover:bg-gray-600 text-white py-2 px-6 rounded-lg transition-colors"
            >
              Cancel
            </button>
          </div>
        </form>
      </div>
      
      <!-- Filter Options -->
      <div class="flex items-center space-x-4 mb-6">
        <div class="flex items-center space-x-2">
          <label class="text-sm font-medium text-gray-700">Filter by status:</label>
          <select
            v-model="statusFilter"
            class="px-3 py-1 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent text-sm"
          >
            <option value="all">All Tasks</option>
            <option value="pending">Pending</option>
            <option value="in-progress">In Progress</option>
            <option value="completed">Completed</option>
          </select>
        </div>
        <div class="text-sm text-gray-600">
          Showing {{ filteredVitalTasks.length }} of {{ vitalTasks.length }} vital tasks
        </div>
      </div>
      
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        <div
          v-for="todo in filteredVitalTasks"
          :key="todo.id"
          :class="[
            'border-2 rounded-lg p-6 hover:shadow-lg transition-all duration-300',
            todo.status === 'completed' 
              ? 'border-green-200 bg-green-50 transform hover:scale-105' 
              : 'border-red-200 bg-red-50 transform hover:scale-105'
          ]"
        >
          <div class="flex items-start justify-between mb-4">
            <div class="flex items-center space-x-2">
              <div :class="[
                'w-3 h-3 rounded-full',
                todo.status === 'completed' ? 'bg-green-500' : 'bg-red-500'
              ]"></div>
              <span :class="[
                'text-xs font-bold uppercase tracking-wide',
                todo.status === 'completed' ? 'text-green-600' : 'text-red-600'
              ]">
                {{ todo.status === 'completed' ? 'Completed - High Priority' : 'High Priority' }}
              </span>
            </div>
            <div class="flex space-x-1">
              <button
                @click="startEdit(todo)"
                :class="[
                  'p-1 rounded hover:bg-opacity-20 transition-colors',
                  todo.status === 'completed' 
                    ? 'text-green-600 hover:bg-green-600' 
                    : 'text-red-600 hover:bg-red-600'
                ]"
              >
                <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM20.71 7.04c.39-.39.39-1.02 0-1.41l-2.34-2.34c-.39-.39-1.02-.39-1.41 0l-1.83 1.83 3.75 3.75 1.83-1.83z"/>
                </svg>
              </button>
              <button
                v-if="todo.status !== 'completed'"
                @click="markAsCompleted(todo)"
                class="p-1 rounded text-green-600 hover:bg-green-600 hover:bg-opacity-20 transition-colors"
                title="Mark as completed"
              >
                <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
                </svg>
              </button>
              <button
                @click="deleteTodo(todo.id)"
                :class="[
                  'p-1 rounded hover:bg-opacity-20 transition-colors',
                  todo.status === 'completed' 
                    ? 'text-green-600 hover:bg-green-600' 
                    : 'text-red-600 hover:bg-red-600'
                ]"
              >
                <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
                </svg>
              </button>
            </div>
          </div>
          
          <h3 :class="[
            'font-bold text-lg mb-2',
            todo.status === 'completed' ? 'text-gray-800 line-through' : 'text-gray-800'
          ]">{{ todo.title }}</h3>
          
          <div v-if="todo.description" class="text-gray-600 text-sm mb-4 line-clamp-3">
            {{ todo.description }}
          </div>
          
          <div class="flex items-center justify-between">
            <span :class="[
              'px-3 py-1 rounded-full text-xs font-medium',
              getStatusClass(todo.status)
            ]">
              {{ formatStatus(todo.status) }}
            </span>
            <span class="text-xs text-gray-500">{{ formatDate(todo.createdAt) }}</span>
          </div>
          
          <!-- Completed Badge -->
          <div v-if="todo.status === 'completed'" class="mt-3 flex items-center justify-center">
            <div class="flex items-center space-x-2 px-3 py-1 bg-green-100 rounded-full">
              <svg class="w-4 h-4 text-green-600" fill="currentColor" viewBox="0 0 24 24">
                <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
              </svg>
              <span class="text-xs font-medium text-green-600">Task Completed</span>
            </div>
          </div>
        </div>
        
        <div v-if="filteredVitalTasks.length === 0" class="col-span-full text-center py-12 text-gray-500">
          <svg class="w-16 h-16 mx-auto mb-4 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
          </svg>
          <p class="text-lg font-medium">No vital tasks found</p>
          <p class="text-sm">{{ statusFilter === 'all' ? 'Create your first vital task to get started' : `No ${statusFilter} vital tasks found` }}</p>
          <button 
            @click="showAddForm = true" 
            class="mt-4 px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors"
          >
            Add a vital task
          </button>
        </div>
      </div>

      <!-- Statistics -->
      <div class="mt-8 grid grid-cols-1 md:grid-cols-4 gap-4">
        <div class="bg-red-50 border border-red-200 rounded-lg p-4 text-center">
          <div class="text-2xl font-bold text-red-600">{{ vitalTasks.length }}</div>
          <div class="text-sm text-red-600">Total Vital</div>
        </div>
        <div class="bg-orange-50 border border-orange-200 rounded-lg p-4 text-center">
          <div class="text-2xl font-bold text-orange-600">{{ pendingVitalTasks.length }}</div>
          <div class="text-sm text-orange-600">Pending</div>
        </div>
        <div class="bg-blue-50 border border-blue-200 rounded-lg p-4 text-center">
          <div class="text-2xl font-bold text-blue-600">{{ inProgressVitalTasks.length }}</div>
          <div class="text-sm text-blue-600">In Progress</div>
        </div>
        <div class="bg-green-50 border border-green-200 rounded-lg p-4 text-center">
          <div class="text-2xl font-bold text-green-600">{{ completedVitalTasks.length }}</div>
          <div class="text-sm text-green-600">Completed</div>
        </div>
      </div>
    </div>
    
    <!-- Edit Modal for Vital Task Page -->
    <div v-if="editingId !== null" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
      <div class="bg-white rounded-lg shadow-xl w-full max-w-md p-6">
        <h3 class="text-lg font-semibold text-gray-800 mb-4">Edit Vital Task</h3>
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Title</label>
            <input
              v-model="editTodo.title"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Description</label>
            <textarea
              v-model="editTodo.description"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent resize-none"
              rows="3"
            ></textarea>
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Status</label>
            <select
              v-model="editTodo.status"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-red-500 focus:border-transparent"
            >
              <option value="pending">Pending</option>
              <option value="in-progress">In Progress</option>
              <option value="completed">Completed</option>
            </select>
          </div>
        </div>
        <div class="flex justify-end space-x-3 mt-6">
          <button
            @click="cancelEdit"
            class="px-4 py-2 border border-gray-300 rounded-lg hover:bg-gray-50 transition-colors"
          >
            Cancel
          </button>
          <button
            @click="updateTodo"
            class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors"
          >
            Save Changes
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VitalTaskPage',
  props: {
    searchQuery: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      todos: [],
      editingId: null,
      editTodo: {},
      showAddForm: false,
      statusFilter: 'all',
      newTask: {
        title: '',
        description: '',
        status: 'pending'
      },
      storageKey: 'todoApp_tasks'
    }
  },
  computed: {
    vitalTasks() {
      return this.todos.filter(todo => todo.priority === 'high')
    },
    pendingVitalTasks() {
      return this.vitalTasks.filter(todo => todo.status === 'pending')
    },
    inProgressVitalTasks() {
      return this.vitalTasks.filter(todo => todo.status === 'in-progress')
    },
    completedVitalTasks() {
      return this.vitalTasks.filter(todo => todo.status === 'completed')
    },
    filteredVitalTasks() {
      let filtered = this.vitalTasks

      // Filter by status
      if (this.statusFilter !== 'all') {
        filtered = filtered.filter(todo => todo.status === this.statusFilter)
      }

      // Filter by search query
      if (this.searchQuery.trim()) {
        const query = this.searchQuery.toLowerCase()
        filtered = filtered.filter(todo => 
          todo.title.toLowerCase().includes(query) ||
          (todo.description && todo.description.toLowerCase().includes(query))
        )
      }

      return filtered
    }
  },
  mounted() {
    this.loadTodos()
  },
  methods: {
    loadTodos() {
      try {
        const stored = localStorage.getItem(this.storageKey)
        if (stored) {
          this.todos = JSON.parse(stored)
        }
      } catch (error) {
        console.error('Error loading todos from localStorage:', error)
        this.todos = []
      }
    },
    saveTodos() {
      try {
        localStorage.setItem(this.storageKey, JSON.stringify(this.todos))
      } catch (error) {
        console.error('Error saving todos to localStorage:', error)
        alert('Error saving data. Please check your browser storage.')
      }
    },
    addVitalTask() {
      if (!this.newTask.title.trim()) return
      
      const newTodo = {
        id: Date.now(),
        title: this.newTask.title.trim(),
        description: this.newTask.description.trim(),
        priority: 'high', // Always high priority for vital tasks
        status: this.newTask.status,
        createdAt: new Date().toISOString(),
        updatedAt: new Date().toISOString()
      }
      
      this.todos.push(newTodo)
      this.saveTodos()
      
      // Reset form
      this.newTask = {
        title: '',
        description: '',
        status: 'pending'
      }
      this.showAddForm = false
    },
    startEdit(todo) {
      this.editingId = todo.id
      this.editTodo = { ...todo }
    },
    updateTodo() {
      const index = this.todos.findIndex(t => t.id === this.editingId)
      if (index !== -1) {
        this.todos[index] = {
          ...this.todos[index],
          ...this.editTodo,
          priority: 'high', // Ensure it stays high priority
          updatedAt: new Date().toISOString()
        }
        this.saveTodos()
        this.cancelEdit()
      }
    },
    cancelEdit() {
      this.editingId = null
      this.editTodo = {}
    },
    deleteTodo(id) {
      if (!confirm('Are you sure you want to delete this vital task?')) return
      
      this.todos = this.todos.filter(t => t.id !== id)
      this.saveTodos()
    },
    markAsCompleted(todo) {
      const index = this.todos.findIndex(t => t.id === todo.id)
      if (index !== -1) {
        this.todos[index] = {
          ...this.todos[index],
          status: 'completed',
          updatedAt: new Date().toISOString()
        }
        this.saveTodos()
      }
    },
    getStatusClass(status) {
      const classes = {
        'pending': 'bg-orange-100 text-orange-800',
        'in-progress': 'bg-blue-100 text-blue-800',
        'completed': 'bg-green-100 text-green-800'
      }
      return classes[status] || classes.pending
    },
    formatStatus(status) {
      return status.split('-').map(word => 
        word.charAt(0).toUpperCase() + word.slice(1)
      ).join(' ')
    },
    formatDate(dateString) {
      if (!dateString) return ''
      return new Date(dateString).toLocaleDateString('en-US', {
        month: 'short',
        day: 'numeric'
      })
    }
  }
}
</script>

<style scoped>
.line-clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
