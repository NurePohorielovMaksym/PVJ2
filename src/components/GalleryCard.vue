<script setup>
defineProps({
  image: { type: Object, required: true },
  isFavorite: { type: Boolean, default: false }
})
defineEmits(['toggle-favorite'])
</script>

<template>
  <div class="card">
    <img :src="image.download_url" :alt="image.author" class="card-img" />
    <div class="card-footer">
      <div class="card-info">
        <span class="author">{{ image.author }}</span>
        <span class="id">#{{ image.id }}</span>
      </div>
      <button class="fav-btn" :class="{ active: isFavorite }" @click="$emit('toggle-favorite')">★</button>
    </div>
  </div>
</template>

<style scoped>
.card { background: #2a2a3a; border-radius: 10px; overflow: hidden; }
.card-img { width: 100%; height: 140px; object-fit: cover; display: block; }
.card-footer { display: flex; justify-content: space-between; align-items: center; padding: 10px; }
.card-info { display: flex; flex-direction: column; font-size: 0.85em; }
.id { color: #888; font-size: 0.8em; }
.fav-btn { background: #333; border: none; border-radius: 50%; width: 28px; height: 28px; color: #888; cursor: pointer; }
.fav-btn.active { background: #8b5cf6; color: #fff; }
</style>