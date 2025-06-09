<template>
  <div class="flex items-center justify-center min-h-[calc(100vh-200px)] p-6">
    <div class="text-center max-w-md mx-auto">
      <!-- Construction Icon -->
      <div class="mb-8">
        <div class="relative inline-block">
          <svg class="w-24 h-24 text-orange-400 mx-auto" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z"/>
          </svg>
          <!-- Animated dots -->
          <div class="absolute -bottom-2 left-1/2 transform -translate-x-1/2 flex space-x-1">
            <div class="w-2 h-2 bg-orange-400 rounded-full animate-bounce" style="animation-delay: 0ms;"></div>
            <div class="w-2 h-2 bg-orange-400 rounded-full animate-bounce" style="animation-delay: 150ms;"></div>
            <div class="w-2 h-2 bg-orange-400 rounded-full animate-bounce" style="animation-delay: 300ms;"></div>
          </div>
        </div>
      </div>

      <!-- Main Message -->
      <div class="mb-6">
        <h1 class="text-3xl font-bold text-gray-800 mb-3">
          {{ getPageTitle() }}
        </h1>
        <h2 class="text-xl font-semibold text-orange-600 mb-4">
          Halaman Sedang Dalam Pengembangan
        </h2>
        <p class="text-gray-600 leading-relaxed">
          Fitur ini sedang dalam tahap pengembangan dan akan segera tersedia. 
          Terima kasih atas kesabaran Anda!
        </p>
      </div>

      <!-- Feature Preview -->
      <div class="bg-gradient-to-r from-orange-50 to-yellow-50 border border-orange-200 rounded-lg p-6 mb-6">
        <h3 class="font-semibold text-gray-800 mb-3">Yang akan datang:</h3>
        <ul class="text-sm text-gray-600 space-y-2">
          <li v-for="feature in getUpcomingFeatures()" :key="feature" class="flex items-center space-x-2">
            <svg class="w-4 h-4 text-orange-500 flex-shrink-0" fill="currentColor" viewBox="0 0 24 24">
              <path d="M9 16.17L4.83 12l-1.42 1.41L9 19 21 7l-1.41-1.41z"/>
            </svg>
            <span>{{ feature }}</span>
          </li>
        </ul>
      </div>

      <!-- Action Buttons -->
      <div class="flex flex-col sm:flex-row gap-3 justify-center">
        <button
          @click="goToDashboard"
          class="flex items-center justify-center space-x-2 px-6 py-3 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors"
        >
          <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
            <path d="M10 20v-6h4v6h5v-8h3L12 3 2 12h3v8z"/>
          </svg>
          <span>Kembali ke Dashboard</span>
        </button>
        <button
          @click="goToMyTasks"
          class="flex items-center justify-center space-x-2 px-6 py-3 bg-blue-500 text-white rounded-lg hover:bg-blue-600 transition-colors"
        >
          <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
            <path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8l-6-6z"/>
          </svg>
          <span>Lihat Semua Task</span>
        </button>
      </div>

      <!-- Progress Indicator -->
      <div class="mt-8 pt-6 border-t border-gray-200">
        <div class="flex items-center justify-center space-x-2 text-sm text-gray-500">
          <svg class="w-4 h-4 animate-spin text-orange-500" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 2v4m0 12v4m10-10h-4M6 12H2m15.364-7.364l-2.828 2.828M9.464 9.464L6.636 6.636m12.728 12.728l-2.828-2.828M9.464 14.536l-2.828 2.828"/>
          </svg>
          <span>Tim pengembang sedang bekerja keras...</span>
        </div>
        
        <!-- Progress Bar -->
        <div class="mt-3 w-full bg-gray-200 rounded-full h-2">
          <div class="bg-gradient-to-r from-orange-400 to-yellow-400 h-2 rounded-full animate-pulse" :style="{ width: getProgressWidth() }"></div>
        </div>
        <p class="text-xs text-gray-500 mt-2">Progress pengembangan: {{ getProgressWidth() }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UnderDevelopmentPage',
  props: {
    searchQuery: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      currentMenu: 'categories' // Default, will be updated by parent
    }
  },
  methods: {
    getPageTitle() {
      // Get current active menu from parent or URL
      const currentPath = this.$parent?.activeMenu || 'categories'
      
      const titles = {
        categories: 'Kategori Task',
        settings: 'Pengaturan',
        help: 'Bantuan & Dukungan'
      }
      
      return titles[currentPath] || 'Fitur Baru'
    },
    
    getUpcomingFeatures() {
      const currentPath = this.$parent?.activeMenu || 'categories'
      
      const features = {
        categories: [
          'Membuat kategori task custom',
          'Mengatur warna untuk setiap kategori',
          'Filter task berdasarkan kategori',
          'Statistik per kategori',
          'Import/export kategori'
        ],
        settings: [
          'Pengaturan tema (Light/Dark mode)',
          'Notifikasi dan reminder',
          'Backup otomatis',
          'Sinkronisasi cloud',
          'Pengaturan bahasa'
        ],
        help: [
          'Tutorial interaktif',
          'FAQ lengkap',
          'Video panduan',
          'Live chat support',
          'Knowledge base'
        ]
      }
      
      return features[currentPath] || [
        'Fitur-fitur menarik',
        'Interface yang lebih baik',
        'Performa yang optimal'
      ]
    },
    
    getProgressWidth() {
      const currentPath = this.$parent?.activeMenu || 'categories'
      
      const progress = {
        categories: '65%',
        settings: '45%',
        help: '30%'
      }
      
      return progress[currentPath] || '50%'
    },
    
    goToDashboard() {
      this.$emit('change-menu', 'dashboard')
    },
    
    goToMyTasks() {
      this.$emit('change-menu', 'mytask')
    }
  }
}
</script>

<style scoped>
@keyframes bounce {
  0%, 20%, 53%, 80%, 100% {
    transform: translateY(0);
  }
  40%, 43% {
    transform: translateY(-8px);
  }
  70% {
    transform: translateY(-4px);
  }
  90% {
    transform: translateY(-2px);
  }
}

.animate-bounce {
  animation: bounce 1.5s infinite;
}
</style>
