<script setup>
import { ref, computed, onMounted } from 'vue'
import GalleryCard from './components/GalleryCard.vue'

const images = ref([])
const isLoading = ref(false)
const error = ref(null)
const favorites = ref([])
const searchQuery = ref('')
const viewMode = ref('all') 

async function fetchImages() {
  isLoading.value = true
  error.value = null
  try {
    const response = await fetch('https://picsum.photos/v2/list?page=1&limit=20')
    if (!response.ok) {
      throw new Error(`Помилка сервера: ${response.status}`)
    }
    const data = await response.json()
    images.value = data
  } catch (err) {
    error.value = err.message || 'Не вдалося завантажити зображення'
  } finally {
    isLoading.value = false
  }
}

function toggleFavorite(id) {
  const index = favorites.value.indexOf(id)
  if (index === -1) {
    favorites.value.push(id)
  } else {
    favorites.value.splice(index, 1)
  }
}

const filteredImages = computed(() => {
  let result = images.value

  if (viewMode.value === 'favorites') {
    result = result.filter(img => favorites.value.includes(img.id))
  }

  if (searchQuery.value.trim()) {
    const query = searchQuery.value.trim().toLowerCase()
    result = result.filter(img => img.author.toLowerCase().includes(query))
  }

  return result
})

onMounted(() => {
  fetchImages()
})
</script>

<template>
  <div class="gallery-app">
    <div class="toolbar">
      <input v-model="searchQuery" placeholder="Пошук за автором" class="search-input" />
      <div class="mode-switch">
        <button :class="{ active: viewMode === 'all' }" @click="viewMode = 'all'">Усі</button>
        <button :class="{ active: viewMode === 'favorites' }" @click="viewMode = 'favorites'">Обрані</button>
      </div>
    </div>

    <p v-if="isLoading" class="status">Завантаження...</p>

    <div v-else-if="error" class="status error">
      <p>{{ error }}</p>
      <button @click="fetchImages">Спробувати ще раз</button>
    </div>

    <div v-else class="grid">
      <GalleryCard
        v-for="image in filteredImages"
        :key="image.id"
        :image="image"
        :is-favorite="favorites.includes(image.id)"
        @toggle-favorite="toggleFavorite(image.id)"
      />
    </div>
  </div>
</template>

<style>
.gallery-app { max-width: 900px; margin: 40px auto; padding: 24px; background: #1e1e2e; border-radius: 12px; color: #fff; font-family: sans-serif; }
.toolbar { display: flex; justify-content: space-between; gap: 12px; margin-bottom: 20px; }
.search-input { flex: 1; padding: 8px 12px; border-radius: 6px; border: none; background: #333; color: #fff; }
.mode-switch { display: flex; gap: 8px; }
.mode-switch button { padding: 6px 14px; border: none; border-radius: 6px; background: #333; color: #fff; cursor: pointer; }
.mode-switch button.active { background: #8b5cf6; }
.status { text-align: center; padding: 40px 0; color: #aaa; }
.status.error button { margin-top: 10px; padding: 6px 14px; border: none; border-radius: 6px; background: #8b5cf6; color: #fff; cursor: pointer; }
.grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 16px; }
</style>