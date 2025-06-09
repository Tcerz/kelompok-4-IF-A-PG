<template>
  <div class="flex h-screen bg-gray-50">
    <!-- Left Sidebar -->
    <div class="w-64 bg-gradient-to-b from-green-500 to-green-600 text-white flex flex-col">
      <!-- User Profile Section -->
      <div class="p-6">
        <div class="flex items-center space-x-3 mb-6">
          <div class="w-12 h-12 bg-white rounded-full flex items-center justify-center">
            <svg class="w-8 h-8 text-green-500" fill="currentColor" viewBox="0 0 24 24">
              <path d="M12 12c2.21 0 4-1.79 4-4s-1.79-4-4-4-4 1.79-4 4 1.79 4 4 4zm0 2c-2.67 0-8 1.34-8 4v2h16v-2c0-2.66-5.33-4-8-4z"/>
            </svg>
          </div>
          <div>
            <h3 class="font-semibold">{{ userName }}</h3>
            <p class="text-green-100 text-sm">{{ userEmail }}</p>
          </div>
        </div>
        
        <h1 class="text-2xl font-bold mb-8">
          <span class="text-white">Dash</span><span class="text-green-100">board</span>
        </h1>
      </div>

      <!-- Navigation Menu -->
      <nav class="flex-1 px-4">
        <ul class="space-y-2">
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'dashboard' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('dashboard')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M3 13h8V3H3v10zm0 8h8v-6H3v6zm10 0h8V11h-8v10zm0-18v6h8V3h-8z"/>
              </svg>
              <span>Dashboard</span>
            </a>
          </li>
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'vital' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('vital')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
              </svg>
              <span>Vital Task</span>
            </a>
          </li>
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'mytask' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('mytask')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8l-6-6z"/>
              </svg>
              <span>My Task</span>
            </a>
          </li>
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'categories' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('categories')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M4 6H2v14c0 1.1.9 2 2 2h14v-2H4V6zm16-4H8c-1.1 0-2 .9-2 2v12c0 1.1.9 2 2 2h12c1.1 0 2-.9 2-2V4c0-1.1-.9-2-2-2z"/>
              </svg>
              <span>Task Categories</span>
            </a>
          </li>
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'settings' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('settings')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M19.14,12.94c0.04-0.3,0.06-0.61,0.06-0.94c0-0.32-0.02-0.64-0.07-0.94l2.03-1.58c0.18-0.14,0.23-0.41,0.12-0.61 l-1.92-3.32c-0.12-0.22-0.37-0.29-0.59-0.22l-2.39,0.96c-0.5-0.38-1.03-0.7-1.62-0.94L14.4,2.81c-0.04-0.24-0.24-0.41-0.48-0.41 h-3.84c-0.24,0-0.43,0.17-0.47,0.41L9.25,5.35C8.66,5.59,8.12,5.92,7.63,6.29L5.24,5.33c-0.22-0.08-0.47,0-0.59,0.22L2.74,8.87 C2.62,9.08,2.66,9.34,2.86,9.48l2.03,1.58C4.84,11.36,4.82,11.69,4.82,12s0.02,0.64,0.07,0.94l-2.03,1.58 c-0.18,0.14-0.23,0.41-0.12,0.61l1.92,3.32c0.12,0.22,0.37,0.29,0.59,0.22l2.39-0.96c0.5,0.38,1.03,0.7,1.62,0.94l0.36,2.54 c0.05,0.24,0.24,0.41,0.48,0.41h3.84c0.24,0,0.44-0.17,0.47-0.41l0.36-2.54c0.59-0.24,1.13-0.56,1.62-0.94l2.39,0.96 c0.22,0.08,0.47,0,0.59-0.22l1.92-3.32c0.12-0.22,0.07-0.47-0.12-0.61L19.14,12.94z M12,15.6c-1.98,0-3.6-1.62-3.6-3.6 s1.62-3.6,3.6-3.6s3.6,1.62,3.6,3.6S13.98,15.6,12,15.6z"/>
              </svg>
              <span>Settings</span>
            </a>
          </li>
          <li>
            <a href="#" :class="[
              'flex items-center space-x-3 px-4 py-3 rounded-lg transition-colors',
              activeMenu === 'help' ? 'bg-white text-green-600' : 'text-white hover:bg-green-500'
            ]" @click="setActiveMenu('help')">
              <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
                <path d="M12,2C6.48,2,2,6.48,2,12s4.48,10,10,10s10-4.48,10-10S17.52,2,12,2z M13,19h-2v-2h2V19z M15.07,11.25l-0.9,0.92 C13.45,12.9,13,13.5,13,15h-2v-0.5c0-1.1,0.45-2.1,1.17-2.83l1.24-1.26c0.37-0.36,0.59-0.86,0.59-1.41c0-1.1-0.9-2-2-2 s-2,0.9-2,2H8c0-2.21,1.79-4,4-4s4,1.79,4,4C16,9.89,15.64,10.67,15.07,11.25z"/>
              </svg>
              <span>Help</span>
            </a>
          </li>
        </ul>
      </nav>

      <!-- Data Management Section -->
      <div class="p-4 border-t border-green-400">
        <div class="space-y-2">
          <button 
            @click="exportData"
            class="flex items-center space-x-3 px-4 py-2 text-white hover:bg-green-500 rounded-lg transition-colors w-full text-sm"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path d="M14,2H6A2,2 0 0,0 4,4V20A2,2 0 0,0 6,22H18A2,2 0 0,0 20,20V8L14,2M18,20H6V4H13V9H18V20Z"/>
            </svg>
            <span>Export Data</span>
          </button>
          <label class="flex items-center space-x-3 px-4 py-2 text-white hover:bg-green-500 rounded-lg transition-colors w-full text-sm cursor-pointer">
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path d="M14,2H6A2,2 0 0,0 4,4V20A2,2 0 0,0 6,22H18A2,2 0 0,0 20,20V8L14,2M18,20H6V4H13V9H18V20Z"/>
            </svg>
            <span>Import Data</span>
            <input type="file" @change="importData" accept=".json" class="hidden">
          </label>
          <button 
            @click="clearAllData"
            class="flex items-center space-x-3 px-4 py-2 text-white hover:bg-red-500 rounded-lg transition-colors w-full text-sm"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path d="M6,19A2,2 0 0,0 8,21H16A2,2 0 0,0 18,19V7H6V19M8,9H16V19H8V9M15.5,4L14.5,3H9.5L8.5,4H5V6H19V4H15.5Z"/>
            </svg>
            <span>Clear All Data</span>
          </button>
        </div>
      </div>

      <!-- Logout Button -->
      <div class="p-4">
        <button class="flex items-center space-x-3 px-4 py-3 text-white hover:bg-green-500 rounded-lg transition-colors w-full">
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
            <path d="M17 7l-1.41 1.41L18.17 11H8v2h10.17l-2.58 2.59L17 17l5-5zM4 5h8V3H4c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h8v-2H4V5z"/>
          </svg>
          <span>Logout</span>
        </button>
      </div>
    </div>

    <!-- Main Content -->
    <div class="flex-1 overflow-auto">
      <!-- Header -->
      <div class="bg-white shadow-sm border-b p-6">
        <div class="flex items-center justify-between">
          <div class="flex items-center space-x-4">
            <h1 class="text-2xl font-bold text-gray-800">
              {{ getPageTitle() }} 👋
            </h1>
          </div>
          <div class="flex items-center space-x-4">
            <!-- Search Bar -->
            <div class="relative">
              <input
                type="text"
                placeholder="Search your task here..."
                class="pl-4 pr-10 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-green-500 focus:border-transparent w-80"
                v-model="searchQuery"
              />
              <button class="absolute right-2 top-2 p-1 bg-green-500 text-white rounded">
                <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
                  <path d="M15.5 14h-.79l-.28-.27C15.41 12.59 16 11.11 16 9.5 16 5.91 13.09 3 9.5 3S3 5.91 3 9.5 5.91 16 9.5 16c1.61 0 3.09-.59 4.23-1.57l.27.28v.79l5 4.99L20.49 19l-4.99-5zm-6 0C7.01 14 5 11.99 5 9.5S7.01 5 9.5 5 14 7.01 14 9.5 11.99 14 9.5 14z"/>
                </svg>
              </button>
            </div>
            <!-- Date -->
            <div class="text-right">
              <div class="text-sm font-semibold text-gray-800">{{ currentDay }}</div>
              <div class="text-xs text-gray-500">{{ currentDate }}</div>
            </div>
          </div>
        </div>
      </div>

      <!-- Dynamic Content Based on Active Menu -->
      <component 
        :is="currentComponent" 
        :search-query="searchQuery"
        @change-menu="setActiveMenu"
      />
    </div>
  </div>
</template>

<script>
import DashboardPage from './DashboardPage.vue'
// import VitalTaskPage from './VitalTaskPage.vue'
// import MyTaskPage from './MyTaskPage.vue'
// import UnderDevelopmentPage from './UnderDevelopmentPage.vue'

export default {
  name: 'TodoApp',
  components: {
    DashboardPage,
    // VitalTaskPage,
    // MyTaskPage,
    // UnderDevelopmentPage
  },
  data() {
    return {
      searchQuery: '',
      activeMenu: 'dashboard',
       userName: 'Fauzan Ramadhan',
      userEmail: 'fauzanramadhan@gmail',
      storageKey: 'todoApp_tasks'
    }
  },
  computed: {
    currentComponent() {
      switch (this.activeMenu) {
        case 'vital':
          return 'VitalTaskPage'
        case 'mytask':
          return 'MyTaskPage'
        case 'dashboard':
          return 'DashboardPage'
        case 'categories':
        case 'settings':
        case 'help':
          return 'UnderDevelopmentPage'
        default:
          return 'DashboardPage'
      }
    },
    currentDay() {
      return new Date().toLocaleDateString('en-US', { weekday: 'long' })
    },
    currentDate() {
      return new Date().toLocaleDateString('en-US', { 
        day: '2-digit',
        month: '2-digit',
        year: 'numeric'
      })
    }
  },
  methods: {
    // Navigation
    setActiveMenu(menu) {
      this.activeMenu = menu
    },
    
    getPageTitle() {
      const titles = {
        dashboard: 'Welcome back, ' + this.userName,
        vital: 'Vital Tasks',
        mytask: 'My Tasks',
        categories: 'Task Categories',
        settings: 'Settings',
        help: 'Help & Support'
      }
      return titles[this.activeMenu] || 'Dashboard'
    },
    
    // Data Management
    exportData() {
      try {
        const stored = localStorage.getItem(this.storageKey)
        const todos = stored ? JSON.parse(stored) : []
        
        const dataStr = JSON.stringify(todos, null, 2)
        const dataBlob = new Blob([dataStr], { type: 'application/json' })
        const url = URL.createObjectURL(dataBlob)
        
        const link = document.createElement('a')
        link.href = url
        link.download = `todo-backup-${new Date().toISOString().split('T')[0]}.json`
        document.body.appendChild(link)
        link.click()
        document.body.removeChild(link)
        URL.revokeObjectURL(url)
        
        alert('Data exported successfully!')
      } catch (error) {
        console.error('Error exporting data:', error)
        alert('Error exporting data.')
      }
    },
    
    importData(event) {
      const file = event.target.files[0]
      if (!file) return
      
      const reader = new FileReader()
      reader.onload = (e) => {
        try {
          const importedData = JSON.parse(e.target.result)
          
          if (Array.isArray(importedData)) {
            if (confirm('This will replace all existing tasks. Continue?')) {
              localStorage.setItem(this.storageKey, JSON.stringify(importedData))
              alert('Data imported successfully! Please refresh the page.')
              location.reload()
            }
          } else {
            alert('Invalid file format. Please select a valid JSON file.')
          }
        } catch (error) {
          console.error('Error importing data:', error)
          alert('Error reading file. Please check the file format.')
        }
      }
      reader.readAsText(file)
      
      // Reset file input
      event.target.value = ''
    },
    
    clearAllData() {
      if (confirm('Are you sure you want to delete ALL tasks? This action cannot be undone.')) {
        localStorage.removeItem(this.storageKey)
        alert('All data cleared successfully! Please refresh the page.')
        location.reload()
      }
    }
  }
}
</script>
