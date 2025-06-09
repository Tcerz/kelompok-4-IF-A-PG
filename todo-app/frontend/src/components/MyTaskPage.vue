<template>
  <div class="p-6">
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6 h-full">
      <!-- Task Description Panel -->
      <div class="bg-white rounded-xl shadow-sm p-6">
        <div class="flex items-center justify-between mb-4">
          <h2 class="text-lg font-semibold text-gray-800">Task Details</h2>
          <button
            v-if="selectedTask"
            @click="selectedTask = null"
            class="p-2 text-gray-400 hover:text-gray-600 rounded-lg hover:bg-gray-100 transition-colors"
            title="Close details"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path d="M19 6.41L17.59 5 12 10.59 6.41 5 5 6.41 10.59 12 5 17.59 6.41 19 12 13.41 17.59 19 19 17.59 13.41 12z"/>
            </svg>
          </button>
        </div>
        
        <div v-if="selectedTask" class="space-y-4">
          <div>
            <h3 class="font-bold text-xl text-gray-800 mb-2">{{ selectedTask.title }}</h3>
            <div class="flex items-center space-x-4 mb-4">
              <span :class="[
                'px-3 py-1 rounded-full text-xs font-medium',
                getPriorityClass(selectedTask.priority)
              ]">
                {{ selectedTask.priority.toUpperCase() }}
              </span>
              <span :class="[
                'px-3 py-1 rounded-full text-xs font-medium',
                getStatusClass(selectedTask.status)
              ]">
                {{ formatStatus(selectedTask.status) }}
              </span>
            </div>
          </div>
          
          <div>
            <h4 class="font-semibold text-gray-700 mb-2">Description</h4>
            <div class="bg-gray-50 rounded-lg p-4">
              <p class="text-gray-600 whitespace-pre-wrap">
                {{ selectedTask.description || 'No description provided.' }}
              </p>
            </div>
          </div>
          
          <div class="grid grid-cols-2 gap-4 text-sm">
            <div>
              <span class="font-semibold text-gray-700">Created:</span>
              <p class="text-gray-600">{{ formatFullDate(selectedTask.createdAt) }}</p>
            </div>
            <div>
              <span class="font-semibold text-gray-700">Updated:</span>
              <p class="text-gray-600">{{ formatFullDate(selectedTask.updatedAt) }}</p>
            </div>
          </div>
          
          <div class="flex space-x-2 pt-4">
            <button
              @click="startEdit(selectedTask)"
              class="flex-1 bg-blue-500 hover:bg-blue-600 text-white py-2 px-4 rounded-lg transition-colors"
            >
              Edit Task
            </button>
            <button
              v-if="selectedTask.status !== 'completed'"
              @click="markAsCompleted(selectedTask)"
              class="flex-1 bg-green-500 hover:bg-green-600 text-white py-2 px-4 rounded-lg transition-colors"
            >
              Mark Complete
            </button>
            <button
              @click="deleteTodo(selectedTask.id)"
              class="flex-1 bg-red-500 hover:bg-red-600 text-white py-2 px-4 rounded-lg transition-colors"
            >
              Delete Task
            </button>
          </div>
        </div>
        
        <div v-else class="text-center py-12 text-gray-500">
          <svg class="w-16 h-16 mx-auto mb-4 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8l-6-6z"/>
          </svg>
          <p class="text-lg font-medium">Select a task</p>
          <p class="text-sm">Click on any task to view its details</p>
        </div>
      </div>
      
      <!-- Task Table -->
      <div class="lg:col-span-2 bg-white rounded-xl shadow-sm p-6">
        <div class="flex items-center justify-between mb-6">
          <div>
            <h2 class="text-lg font-semibold text-gray-800">All Tasks</h2>
            <p class="text-gray-600">{{ filteredAllTasks.length }} total tasks</p>
          </div>
          <div class="flex items-center space-x-4">
            <!-- Filter Controls -->
            <div class="flex items-center space-x-2">
              <label class="text-sm font-medium text-gray-700">Priority:</label>
              <select
                v-model="priorityFilter"
                class="px-3 py-1 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent text-sm"
              >
                <option value="all">All</option>
                <option value="high">High</option>
                <option value="medium">Medium</option>
                <option value="low">Low</option>
              </select>
            </div>
            <div class="flex items-center space-x-2">
              <label class="text-sm font-medium text-gray-700">Status:</label>
              <select
                v-model="statusFilter"
                class="px-3 py-1 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent text-sm"
              >
                <option value="all">All</option>
                <option value="pending">Pending</option>
                <option value="in-progress">In Progress</option>
                <option value="completed">Completed</option>
              </select>
            </div>
            <button
              @click="showAddForm = !showAddForm"
              class="flex items-center space-x-2 px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
            >
              <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                <path d="M19 13h-6v6h-2v-6H5v-2h6V5h2v6h6v2z"/>
              </svg>
              <span>Add Task</span>
            </button>
          </div>
        </div>
        
        <!-- Add Task Form -->
        <div v-if="showAddForm" class="mb-6 p-4 bg-blue-50 border border-blue-200 rounded-lg">
          <h3 class="text-lg font-semibold text-blue-800 mb-4">Add New Task</h3>
          <form @submit.prevent="addTodo" class="space-y-4">
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
              <input
                v-model="newTodo.title"
                type="text"
                placeholder="Task title..."
                class="px-3 py-2 border border-blue-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none"
                required
              />
              <select
                v-model="newTodo.priority"
                class="px-3 py-2 border border-blue-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none"
              >
                <option value="low">Low Priority</option>
                <option value="medium">Medium Priority</option>
                <option value="high">High Priority</option>
              </select>
              <select
                v-model="newTodo.status"
                class="px-3 py-2 border border-blue-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none"
              >
                <option value="pending">Pending</option>
                <option value="in-progress">In Progress</option>
                <option value="completed">Completed</option>
              </select>
            </div>
            <textarea
              v-model="newTodo.description"
              placeholder="Task description..."
              class="w-full px-3 py-2 border border-blue-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none resize-none"
              rows="2"
            ></textarea>
            <div class="flex space-x-2">
              <button
                type="submit"
                class="bg-blue-500 hover:bg-blue-600 text-white py-2 px-4 rounded-lg transition-colors"
              >
                Add Task
              </button>
              <button
                type="button"
                @click="showAddForm = false"
                class="bg-gray-500 hover:bg-gray-600 text-white py-2 px-4 rounded-lg transition-colors"
              >
                Cancel
              </button>
            </div>
          </form>
        </div>
        
        <!-- Task Statistics -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
          <div class="bg-gray-50 border border-gray-200 rounded-lg p-4 text-center">
            <div class="text-2xl font-bold text-gray-600">{{ todos.length }}</div>
            <div class="text-sm text-gray-600">Total Tasks</div>
          </div>
          <div class="bg-orange-50 border border-orange-200 rounded-lg p-4 text-center">
            <div class="text-2xl font-bold text-orange-600">{{ pendingTasks.length }}</div>
            <div class="text-sm text-orange-600">Pending</div>
          </div>
          <div class="bg-blue-50 border border-blue-200 rounded-lg p-4 text-center">
            <div class="text-2xl font-bold text-blue-600">{{ inProgressTasks.length }}</div>
            <div class="text-sm text-blue-600">In Progress</div>
          </div>
          <div class="bg-green-50 border border-green-200 rounded-lg p-4 text-center">
            <div class="text-2xl font-bold text-green-600">{{ completedTasks.length }}</div>
            <div class="text-sm text-green-600">Completed</div>
          </div>
        </div>
        
        <!-- Task Table -->
        <div class="overflow-x-auto">
          <table class="w-full">
            <thead>
              <tr class="border-b border-gray-200">
                <th class="text-left py-3 px-4 font-semibold text-gray-700">
                  <button @click="sortBy('title')" class="flex items-center space-x-1 hover:text-blue-600">
                    <span>Task</span>
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M7 10l5 5 5-5z"/>
                    </svg>
                  </button>
                </th>
                <th class="text-left py-3 px-4 font-semibold text-gray-700">
                  <button @click="sortBy('priority')" class="flex items-center space-x-1 hover:text-blue-600">
                    <span>Priority</span>
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M7 10l5 5 5-5z"/>
                    </svg>
                  </button>
                </th>
                <th class="text-left py-3 px-4 font-semibold text-gray-700">
                  <button @click="sortBy('status')" class="flex items-center space-x-1 hover:text-blue-600">
                    <span>Status</span>
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M7 10l5 5 5-5z"/>
                    </svg>
                  </button>
                </th>
                <th class="text-left py-3 px-4 font-semibold text-gray-700">
                  <button @click="sortBy('createdAt')" class="flex items-center space-x-1 hover:text-blue-600">
                    <span>Created</span>
                    <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                      <path d="M7 10l5 5 5-5z"/>
                    </svg>
                  </button>
                </th>
                <th class="text-left py-3 px-4 font-semibold text-gray-700">Actions</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="todo in paginatedTasks"
                :key="todo.id"
                :class="[
                  'border-b border-gray-100 hover:bg-gray-50 cursor-pointer transition-colors',
                  selectedTask && selectedTask.id === todo.id ? 'bg-blue-50 border-blue-200' : ''
                ]"
                @click="selectTask(todo)"
              >
                <td class="py-3 px-4">
                  <div class="font-medium text-gray-800">{{ todo.title }}</div>
                  <div v-if="todo.description" class="text-sm text-gray-500 line-clamp-1">
                    {{ todo.description }}
                  </div>
                </td>
                <td class="py-3 px-4">
                  <span :class="[
                    'px-2 py-1 rounded-full text-xs font-medium',
                    getPriorityClass(todo.priority)
                  ]">
                    {{ todo.priority }}
                  </span>
                </td>
                <td class="py-3 px-4">
                  <span :class="[
                    'px-2 py-1 rounded-full text-xs font-medium',
                    getStatusClass(todo.status)
                  ]">
                    {{ formatStatus(todo.status) }}
                  </span>
                </td>
                <td class="py-3 px-4 text-sm text-gray-500">
                  {{ formatDate(todo.createdAt) }}
                </td>
                <td class="py-3 px-4">
                  <div class="flex space-x-1">
                    <button
                      @click.stop="startEdit(todo)"
                      class="p-1 text-blue-600 hover:bg-blue-50 rounded transition-colors"
                      title="Edit task"
                    >
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path d="M3 17.25V21h3.75L17.81 9.94l-3.75-3.75L3 17.25zM20.71 7.04c.39-.39.39-1.02 0-1.41l-2.34-2.34c-.39-.39-1.02-.39-1.41 0l-1.83 1.83 3.75 3.75 1.83-1.83z"/>
                      </svg>
                    </button>
                    <button
                      v-if="todo.status !== 'completed'"
                      @click.stop="markAsCompleted(todo)"
                      class="p-1 text-green-600 hover:bg-green-50 rounded transition-colors"
                      title="Mark as completed"
                    >
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
                      </svg>
                    </button>
                    <button
                      @click.stop="deleteTodo(todo.id)"
                      class="p-1 text-red-600 hover:bg-red-50 rounded transition-colors"
                      title="Delete task"
                    >
                      <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                        <path d="M6 19c0 1.1.9 2 2 2h8c1.1 0 2-.9 2-2V7H6v12zM19 4h-3.5l-1-1h-5l-1 1H5v2h14V4z"/>
                      </svg>
                    </button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>
          
          <div v-if="filteredAllTasks.length === 0" class="text-center py-12 text-gray-500">
            <svg class="w-16 h-16 mx-auto mb-4 text-gray-300" fill="currentColor" viewBox="0 0 24 24">
              <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8l-6-6z"/>
            </svg>
            <p class="text-lg font-medium">No tasks found</p>
            <p class="text-sm">Create your first task to get started</p>
          </div>
        </div>

        <!-- Pagination -->
        <div v-if="totalPages > 1" class="flex items-center justify-between mt-6">
          <div class="text-sm text-gray-700">
            Showing {{ (currentPage - 1) * itemsPerPage + 1 }} to {{ Math.min(currentPage * itemsPerPage, filteredAllTasks.length) }} of {{ filteredAllTasks.length }} tasks
          </div>
          <div class="flex items-center space-x-2">
            <button
              @click="currentPage = Math.max(1, currentPage - 1)"
              :disabled="currentPage === 1"
              class="px-3 py-1 border border-gray-300 rounded-lg hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed"
            >
              Previous
            </button>
            <span class="px-3 py-1 bg-blue-500 text-white rounded-lg">{{ currentPage }}</span>
            <button
              @click="currentPage = Math.min(totalPages, currentPage + 1)"
              :disabled="currentPage === totalPages"
              class="px-3 py-1 border border-gray-300 rounded-lg hover:bg-gray-50 disabled:opacity-50 disabled:cursor-not-allowed"
            >
              Next
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Edit Modal -->
    <div v-if="editingId !== null" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
      <div class="bg-white rounded-lg shadow-xl w-full max-w-md p-6">
        <h3 class="text-lg font-semibold text-gray-800 mb-4">Edit Task</h3>
        <div class="space-y-4">
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Title</label>
            <input
              v-model="editTodo.title"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            />
          </div>
          <div>
            <label class="block text-sm font-medium text-gray-700 mb-1">Description</label>
            <textarea
              v-model="editTodo.description"
              class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent resize-none"
              rows="3"
            ></textarea>
          </div>
          <div class="grid grid-cols-2 gap-4">
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Priority</label>
              <select
                v-model="editTodo.priority"
                class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
                <option value="low">Low</option>
                <option value="medium">Medium</option>
                <option value="high">High</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-gray-700 mb-1">Status</label>
              <select
                v-model="editTodo.status"
                class="w-full px-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
              >
                <option value="pending">Pending</option>
                <option value="in-progress">In Progress</option>
                <option value="completed">Completed</option>
              </select>
            </div>
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
            class="px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
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
  name: 'MyTaskPage',
  props: {
    searchQuery: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      todos: [],
      selectedTask: null,
      editingId: null,
      editTodo: {},
      showAddForm: false,
      priorityFilter: 'all',
      statusFilter: 'all',
      sortField: 'createdAt',
      sortDirection: 'desc',
      currentPage: 1,
      itemsPerPage: 10,
      newTodo: {
        title: '',
        description: '',
        priority: 'medium',
        status: 'pending'
      },
      storageKey: 'todoApp_tasks'
    }
  },
  computed: {
    pendingTasks() {
      return this.todos.filter(todo => todo.status === 'pending')
    },
    inProgressTasks() {
      return this.todos.filter(todo => todo.status === 'in-progress')
    },
    completedTasks() {
      return this.todos.filter(todo => todo.status === 'completed')
    },
    filteredAllTasks() {
      let filtered = [...this.todos]

      // Filter by priority
      if (this.priorityFilter !== 'all') {
        filtered = filtered.filter(todo => todo.priority === this.priorityFilter)
      }

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

      // Sort
      filtered.sort((a, b) => {
        let aValue = a[this.sortField]
        let bValue = b[this.sortField]

        if (this.sortField === 'priority') {
          const priorityOrder = { high: 3, medium: 2, low: 1 }
          aValue = priorityOrder[aValue]
          bValue = priorityOrder[bValue]
        }

        if (aValue < bValue) return this.sortDirection === 'asc' ? -1 : 1
        if (aValue > bValue) return this.sortDirection === 'asc' ? 1 : -1
        return 0
      })

      return filtered
    },
    paginatedTasks() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return this.filteredAllTasks.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.filteredAllTasks.length / this.itemsPerPage)
    }
  },
  mounted() {
    this.loadTodos()
  },
  watch: {
    filteredAllTasks() {
      // Reset to first page when filters change
      this.currentPage = 1
    }
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
    selectTask(task) {
      this.selectedTask = task
    },
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
          updatedAt: new Date().toISOString()
        }
        this.saveTodos()
        
        // Update selected task if it's the one being edited
        if (this.selectedTask && this.selectedTask.id === this.editingId) {
          this.selectedTask = this.todos[index]
        }
        
        this.cancelEdit()
      }
    },
    cancelEdit() {
      this.editingId = null
      this.editTodo = {}
    },
    deleteTodo(id) {
      if (!confirm('Are you sure you want to delete this task?')) return
      
      this.todos = this.todos.filter(t => t.id !== id)
      this.saveTodos()
      
      // Clear selected task if it's the one being deleted
      if (this.selectedTask && this.selectedTask.id === id) {
        this.selectedTask = null
      }
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
        
        // Update selected task if it's the one being completed
        if (this.selectedTask && this.selectedTask.id === todo.id) {
          this.selectedTask = this.todos[index]
        }
      }
    },
    sortBy(field) {
      if (this.sortField === field) {
        this.sortDirection = this.sortDirection === 'asc' ? 'desc' : 'asc'
      } else {
        this.sortField = field
        this.sortDirection = 'asc'
      }
    },
    getPriorityClass(priority) {
      const classes = {
        low: 'bg-green-100 text-green-800',
        medium: 'bg-yellow-100 text-yellow-800',
        high: 'bg-red-100 text-red-800'
      }
      return classes[priority] || classes.medium
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
    },
    formatFullDate(dateString) {
      if (!dateString) return ''
      return new Date(dateString).toLocaleDateString('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        hour: '2-digit',
        minute: '2-digit'
      })
    }
  }
}
</script>

<style scoped>
.line-clamp-1 {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>
