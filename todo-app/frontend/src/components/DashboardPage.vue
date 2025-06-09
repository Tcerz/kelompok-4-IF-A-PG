<template>
  <div class="p-6 space-y-6">
    <!-- Add Task Section -->
    <div class="bg-white rounded-xl shadow-sm p-6">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-semibold text-gray-800">Add New Task</h2>
        <button
          @click="showAddForm = !showAddForm"
          class="flex items-center space-x-2 px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors"
        >
          <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
            <path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z"/>
          </svg>
          <span>Add Task</span>
        </button>
      </div>
      
      <form v-if="showAddForm" @submit.prevent="addTodo" class="space-y-4">
        <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
          <input
            v-model="newTodo.title"
            type="text"
            placeholder="Task title..."
            class="px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none"
            required
          />
          <select
            v-model="newTodo.priority"
            class="px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none"
          >
            <option value="low">Low Priority</option>
            <option value="medium">Medium Priority</option>
            <option value="high">High Priority</option>
          </select>
          <select
            v-model="newTodo.status"
            class="px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none"
          >
            <option value="pending">Pending</option>
            <option value="in-progress">In Progress</option>
            <option value="completed">Completed</option>
          </select>
        </div>
        <div class="mt-4">
          <textarea
            v-model="newTodo.description"
            placeholder="Task description..."
            class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent outline-none resize-none"
            rows="3"
          ></textarea>
        </div>
        <button
          type="submit"
          class="w-full bg-green-500 hover:bg-green-600 text-white font-semibold py-3 px-6 rounded-lg transition-colors"
        >
          Add Task
        </button>
      </form>
    </div>

    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- To-Do Section -->
      <div class="bg-white rounded-xl shadow-sm p-6">
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-semibold text-gray-800">To-Do</h2>
          <span class="text-sm text-gray-500">{{ pendingTodos.length }} Tasks</span>
        </div>
        
        <div class="space-y-3 max-h-96 overflow-y-auto">
          <div
            v-for="todo in filteredPendingTodos"
            :key="todo.id"
            class="border border-gray-200 rounded-lg p-4 hover:shadow-md transition-shadow"
          >
            <div v-if="editingId !== todo.id">
              <div class="flex items-start justify-between mb-2">
                <h3 class="font-semibold text-gray-800 text-sm">{{ todo.title }}</h3>
                <div class="flex space-x-1">
                  <button
                    @click="startEdit(todo)"
                    class="p-1 text-green-600 hover:bg-green-50 rounded"
                  >
                    <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM20.71 7.04c.39-.39.39-1.02 0-1.41l-2.34-2.34c-.39-.39-1.02-.39-1.41 0l-1.83 1.83 3.75 3.75 1.83-1.83z"/>
                    </svg>
                  </button>
                  <button
                    @click="deleteTodo(todo.id)"
                    class="p-1 text-red-600 hover:bg-red-50 rounded"
                  >
                    <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
                    </svg>
                  </button>
                </div>
              </div>
              <div class="flex items-center justify-between">
                <span :class="[
                  'px-2 py-1 rounded-full text-xs font-medium',
                  getPriorityClass(todo.priority)
                ]">
                  {{ todo.priority }}
                </span>
                <span class="text-xs text-gray-500">{{ formatDate(todo.createdAt) }}</span>
              </div>
              <div v-if="todo.description" class="mt-2 text-xs text-gray-600 line-clamp-2">
                {{ todo.description }}
              </div>
            </div>
            
            <!-- Edit Form -->
            <div v-else class="space-y-2">
              <input
                v-model="editTodo.title"
                class="w-full px-2 py-1 text-sm border rounded"
              />
              <textarea
                v-model="editTodo.description"
                class="w-full px-2 py-1 text-sm border rounded resize-none"
                rows="2"
              ></textarea>
              <div class="flex space-x-2">
                <select v-model="editTodo.priority" class="flex-1 px-2 py-1 text-xs border rounded">
                  <option value="low">Low</option>
                  <option value="medium">Medium</option>
                  <option value="high">High</option>
                </select>
                <select v-model="editTodo.status" class="flex-1 px-2 py-1 text-xs border rounded">
                  <option value="pending">Pending</option>
                  <option value="in-progress">In Progress</option>
                  <option value="completed">Completed</option>
                </select>
              </div>
              <div class="flex space-x-2">
                <button
                  @click="updateTodo"
                  class="px-2 py-1 bg-green-500 text-white text-xs rounded"
                >
                  Save
                </button>
                <button
                  @click="cancelEdit"
                  class="px-2 py-1 bg-gray-500 text-white text-xs rounded"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
          
          <div v-if="filteredPendingTodos.length === 0" class="text-center py-8 text-gray-500">
            <svg class="w-12 h-12 mx-auto mb-2 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
              <path d="M9 11H7v6h2v-6zm4 0h-2v6h2v-6zm4 0h-2v6h2v-6zm2-7h-3V2h-2v2H8V2H6v2H3c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V6c0-1.1-.9-2-2-2zm0 16H3V8h14v12z"/>
            </svg>
            <p class="text-sm">No pending tasks</p>
          </div>
        </div>
      </div>

      <!-- In Progress Section -->
      <div class="bg-white rounded-xl shadow-sm p-6">
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-semibold text-gray-800">In Progress</h2>
          <span class="text-sm text-gray-500">{{ inProgressTodos.length }} Tasks</span>
        </div>
        
        <div class="space-y-3 max-h-96 overflow-y-auto">
          <div
            v-for="todo in filteredInProgressTodos"
            :key="todo.id"
            class="border border-blue-200 bg-blue-50 rounded-lg p-4 hover:shadow-md transition-shadow"
          >
            <div v-if="editingId !== todo.id">
              <div class="flex items-start justify-between mb-2">
                <h3 class="font-semibold text-gray-800 text-sm">{{ todo.title }}</h3>
                <div class="flex space-x-1">
                  <button
                    @click="startEdit(todo)"
                    class="p-1 text-blue-600 hover:bg-blue-100 rounded"
                  >
                    <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM20.71 7.04c.39-.39.39-1.02 0-1.41l-2.34-2.34c-.39-.39-1.02-.39-1.41 0l-1.83 1.83 3.75 3.75 1.83-1.83z"/>
                    </svg>
                  </button>
                  <button
                    @click="deleteTodo(todo.id)"
                    class="p-1 text-red-600 hover:bg-red-50 rounded"
                  >
                    <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
                    </svg>
                  </button>
                </div>
              </div>
              <div class="flex items-center justify-between">
                <span :class="[
                  'px-2 py-1 rounded-full text-xs font-medium',
                  getPriorityClass(todo.priority)
                ]">
                  {{ todo.priority }}
                </span>
                <span class="text-xs text-gray-500">{{ formatDate(todo.createdAt) }}</span>
              </div>
              <div v-if="todo.description" class="mt-2 text-xs text-gray-600 line-clamp-2">
                {{ todo.description }}
              </div>
            </div>
            
            <!-- Edit Form -->
            <div v-else class="space-y-2">
              <input
                v-model="editTodo.title"
                class="w-full px-2 py-1 text-sm border rounded"
              />
              <textarea
                v-model="editTodo.description"
                class="w-full px-2 py-1 text-sm border rounded resize-none"
                rows="2"
              ></textarea>
              <div class="flex space-x-2">
                <select v-model="editTodo.priority" class="flex-1 px-2 py-1 text-xs border rounded">
                  <option value="low">Low</option>
                  <option value="medium">Medium</option>
                  <option value="high">High</option>
                </select>
                <select v-model="editTodo.status" class="flex-1 px-2 py-1 text-xs border rounded">
                  <option value="pending">Pending</option>
                  <option value="in-progress">In Progress</option>
                  <option value="completed">Completed</option>
                </select>
              </div>
              <div class="flex space-x-2">
                <button
                  @click="updateTodo"
                  class="px-2 py-1 bg-green-500 text-white text-xs rounded"
                >
                  Save
                </button>
                <button
                  @click="cancelEdit"
                  class="px-2 py-1 bg-gray-500 text-white text-xs rounded"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>
          
          <div v-if="filteredInProgressTodos.length === 0" class="text-center py-8 text-gray-500">
            <svg class="w-12 h-12 mx-auto mb-2 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm-2 15l-5-5 1.41-1.41L10 14.17l7.59-7.59L19 8l-9 9z"/>
            </svg>
            <p class="text-sm">No tasks in progress</p>
          </div>
        </div>
      </div>

      <!-- Task Status (Progress Circles) -->
      <div class="bg-white rounded-xl shadow-sm p-6">
        <div class="flex items-center justify-between mb-6">
          <h2 class="text-lg font-semibold text-gray-800">Task Status</h2>
        </div>
        
        <div class="space-y-6">
          <!-- Completed Circle -->
          <div class="flex items-center justify-center">
            <div class="relative w-24 h-24">
              <svg class="w-24 h-24 transform -rotate-90" viewBox="0 0 100 100">
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#e5e7eb"
                  stroke-width="8"
                  fill="none"
                />
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#10b981"
                  stroke-width="8"
                  fill="none"
                  :stroke-dasharray="circumference"
                  :stroke-dashoffset="circumference - (completedPercentage / 100) * circumference"
                  class="transition-all duration-300"
                />
              </svg>
              <div class="absolute inset-0 flex items-center justify-center">
                <span class="text-lg font-bold text-green-600">{{ Math.round(completedPercentage) }}%</span>
              </div>
            </div>
          </div>
          <div class="text-center">
            <div class="flex items-center justify-center space-x-2">
              <div class="w-3 h-3 bg-green-500 rounded-full"></div>
              <span class="text-sm font-medium text-gray-700">Completed</span>
            </div>
          </div>

          <!-- In Progress Circle -->
          <div class="flex items-center justify-center">
            <div class="relative w-24 h-24">
              <svg class="w-24 h-24 transform -rotate-90" viewBox="0 0 100 100">
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#e5e7eb"
                  stroke-width="8"
                  fill="none"
                />
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#14b8a6"
                  stroke-width="8"
                  fill="none"
                  :stroke-dasharray="circumference"
                  :stroke-dashoffset="circumference - (inProgressPercentage / 100) * circumference"
                  class="transition-all duration-300"
                />
              </svg>
              <div class="absolute inset-0 flex items-center justify-center">
                <span class="text-lg font-bold text-teal-600">{{ Math.round(inProgressPercentage) }}%</span>
              </div>
            </div>
          </div>
          <div class="text-center">
            <div class="flex items-center justify-center space-x-2">
              <div class="w-3 h-3 bg-teal-500 rounded-full"></div>
              <span class="text-sm font-medium text-gray-700">In Progress</span>
            </div>
          </div>

          <!-- Pending Circle -->
          <div class="flex items-center justify-center">
            <div class="relative w-24 h-24">
              <svg class="w-24 h-24 transform -rotate-90" viewBox="0 0 100 100">
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#e5e7eb"
                  stroke-width="8"
                  fill="none"
                />
                <circle
                  cx="50"
                  cy="50"
                  r="40"
                  stroke="#f97316"
                  stroke-width="8"
                  fill="none"
                  :stroke-dasharray="circumference"
                  :stroke-dashoffset="circumference - (pendingPercentage / 100) * circumference"
                  class="transition-all duration-300"
                />
              </svg>
              <div class="absolute inset-0 flex items-center justify-center">
                <span class="text-lg font-bold text-orange-600">{{ Math.round(pendingPercentage) }}%</span>
              </div>
            </div>
          </div>
          <div class="text-center">
            <div class="flex items-center justify-center space-x-2">
              <div class="w-3 h-3 bg-orange-500 rounded-full"></div>
              <span class="text-sm font-medium text-gray-700">Not Started</span>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Completed Tasks Section -->
    <div class="bg-white rounded-xl shadow-sm p-6">
      <div class="flex items-center justify-between mb-4">
        <h2 class="text-lg font-semibold text-gray-800">Completed Tasks</h2>
        <span class="text-sm text-gray-500">{{ completedTodos.length }} Tasks</span>
      </div>
      
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
        <div
          v-for="todo in filteredCompletedTodos"
          :key="todo.id"
          class="border border-green-200 bg-green-50 rounded-lg p-4 hover:shadow-md transition-shadow"
        >
          <div class="flex items-start space-x-3">
            <div class="flex-shrink-0 mt-1">
              <div class="w-6 h-6 bg-green-500 rounded-full flex items-center justify-center">
                <svg class="w-4 h-4 text-white" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
                </svg>
              </div>
            </div>
            <div class="flex-1 min-w-0">
              <h3 class="font-semibold text-gray-800 text-sm mb-1">{{ todo.title }}</h3>
              <div class="flex items-center justify-between">
                <span class="text-xs text-green-600 font-medium">Completed</span>
                <span class="text-xs text-gray-500">{{ formatDate(todo.createdAt) }}</span>
              </div>
              <div v-if="todo.description" class="mt-1 text-xs text-gray-500 line-clamp-2">
                {{ todo.description }}
              </div>
            </div>
            <button
              @click="deleteTodo(todo.id)"
              class="p-1 text-red-600 hover:bg-red-50 rounded"
            >
              <svg class="w-3 h-3" fill="currentColor" viewBox="0 0 24 24">
                <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
              </svg>
            </button>
          </div>
        </div>
        
        <div v-if="filteredCompletedTodos.length === 0" class="col-span-full text-center py-8 text-gray-500">
          <svg class="w-12 h-12 mx-auto mb-2 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
            <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
          </svg>
          <p class="text-sm">No completed tasks yet</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DashboardPage',
  props: {
    searchQuery: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      todos: [],
      newTodo: {
        title: '',
        description: '',
        priority: 'medium',
        status: 'pending'
      },
      editingId: null,
      editTodo: {},
      showAddForm: false,
      circumference: 2 * Math.PI * 40,
      storageKey: 'todoApp_tasks'
    }
  },
  computed: {
    pendingTodos() {
      return this.todos.filter(todo => todo.status === 'pending')
    },
    inProgressTodos() {
      return this.todos.filter(todo => todo.status === 'in-progress')
    },
    completedTodos() {
      return this.todos.filter(todo => todo.status === 'completed')
    },
    filteredPendingTodos() {
      return this.filterTodos(this.pendingTodos)
    },
    filteredInProgressTodos() {
      return this.filterTodos(this.inProgressTodos)
    },
    filteredCompletedTodos() {
      return this.filterTodos(this.completedTodos)
    },
    completedPercentage() {
      if (this.todos.length === 0) return 0
      return (this.completedTodos.length / this.todos.length) * 100
    },
    inProgressPercentage() {
      if (this.todos.length === 0) return 0
      return (this.inProgressTodos.length / this.todos.length) * 100
    },
    pendingPercentage() {
      if (this.todos.length === 0) return 0
      return (this.pendingTodos.length / this.todos.length) * 100
    }
  },
  mounted() {
    this.loadTodos()
  },
  methods: {
    // LocalStorage Methods
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
    
    // Todo CRUD Methods
    addTodo() {
      if (!this.newTodo.title.trim()) return
      
      const newTodo = {
        id: Date.now(),
        title: this.newTodo.title.trim(),
        description: this.newTodo.description.trim(),
        priority: this.newTodo.priority,
        status: this.newTodo.status,
        createdAt: new Date().toISOString(),
        updatedAt: new Date().toISOString()
      }
      
      this.todos.push(newTodo)
      this.saveTodos()
      
      // Reset form
      this.newTodo = {
        title: '',
        description: '',
        priority: 'medium',
        status: 'pending'
      }
      this.showAddForm = false
    },
    
    updateTodo() {
      const index = this.todos.findIndex(t => t.id === this.editingId)
      if (index !== -1) {
        this.todos[index] = {
          ...this.todos[index],
          ...this.editTodo,
          updatedAt: new Date().toISOString()
        }
        this.saveTodos()
        this.cancelEdit()
      }
    },
    
    deleteTodo(id) {
      if (!confirm('Are you sure you want to delete this task?')) return
      
      this.todos = this.todos.filter(t => t.id !== id)
      this.saveTodos()
    },
    
    startEdit(todo) {
      this.editingId = todo.id
      this.editTodo = { ...todo }
    },
    
    cancelEdit() {
      this.editingId = null
      this.editTodo = {}
    },
    
    // Search and Filter
    filterTodos(todos) {
      if (!this.searchQuery.trim()) return todos
      
      const query = this.searchQuery.toLowerCase()
      return todos.filter(todo => 
        todo.title.toLowerCase().includes(query) ||
        (todo.description && todo.description.toLowerCase().includes(query))
      )
    },
    
    // Utility Methods
    getPriorityClass(priority) {
      const classes = {
        low: 'bg-green-100 text-green-800',
        medium: 'bg-yellow-100 text-yellow-800',
        high: 'bg-red-100 text-red-800'
      }
      return classes[priority] || classes.medium
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
.line-clamp-2 {
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
