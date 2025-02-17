<template>
  <div class="app">
    <!-- Search Bar (overlapping the grid) -->
    <header class="search-bar">
      <input
        type="text"
        v-model="searchQuery"
        placeholder="Search photos..."
        @keyup.enter="fetchPhotos"
      />
      <button @click="fetchPhotos">Search</button>
    </header>

    <!-- Staggered grid container -->
    <div class="grid-container">
      <!-- Loading placeholders -->
      <div
        v-if="loading"
        class="loading-placeholder"
        v-for="n in 6"
        :key="n"
      ></div>
      <!-- Photo Cards -->
      <div
        v-else
        class="photo-card"
        v-for="photo in photos"
        :key="photo.id"
        @click="openModal(photo)"
      >
        <img :src="photo.urls.small" :alt="photo.alt_description || 'Photo'" />
        <!-- Tint overlay for text -->
        <div class="photo-overlay">
          <p class="author">{{ photo.user.name }}</p>
          <p class="location">
            {{
              photo.location && photo.location.name
                ? photo.location.name
                : "Unknown Location"
            }}
          </p>
        </div>
      </div>
    </div>

    <!-- Photo Modal -->
    <PhotoModal v-if="showModal" :photo="selectedPhoto" @close="closeModal" />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";
import axios from "axios";
import PhotoModal from "./components/PhotoModal.vue";

const searchQuery = ref("");
const photos = ref([]);
const loading = ref(false);
const showModal = ref(false);
const selectedPhoto = ref(null);

const UNSPLASH_API_KEY =
  "VQ7AaQD1HlMxMsMWHWRTEpA-etqNYzlZvrgsB99QBto";

  const fetchPhotos = async () => {
  loading.value = true;
  try {
    let response;
    if (searchQuery.value) {
      response = await axios.get("https://api.unsplash.com/search/photos", {
        params: { query: searchQuery.value, per_page: 30 },
        headers: { Authorization: `Client-ID ${UNSPLASH_API_KEY}` },
      });
      photos.value = response.data.results;
    } else {
      response = await axios.get("https://api.unsplash.com/search/photos", {
        params: { query: "Africa", per_page: 30 },
        headers: { Authorization: `Client-ID ${UNSPLASH_API_KEY}` },
      });
      photos.value = response.data.results;
    }
  } catch (error) {
    console.error("Error fetching photos:", error);
  } finally {
    loading.value = false;
  }
};

const openModal = (photo) => {
  selectedPhoto.value = photo;
  showModal.value = true;
};

const closeModal = () => {
  showModal.value = false;
  selectedPhoto.value = null;
};

onMounted(() => {
  fetchPhotos();
});
</script>

<style scoped>
:root {
  --overlay-color: rgba(0, 0, 0, 0.4);
}

.app {
  font-family: Arial, sans-serif;
  background: #fafafa;
  padding-bottom: 50px;
}

.search-bar {
  position: relative;
  background: #fff;
  padding: 20px;
  z-index: 10;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  display: flex;
  gap: 10px;
}

.search-bar input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.search-bar button {
  padding: 10px 20px;
  background: #ff6f00;
  border: none;
  color: #fff;
  cursor: pointer;
  border-radius: 4px;
  transition: background 0.3s ease;
}

.search-bar button:hover {
  background: #e55a00;
}

.grid-container {
  column-count: 3;
  column-gap: 1rem;
  margin-top: -30px;
  padding: 20px;
}

.photo-card {
  position: relative;
  margin-bottom: 1rem;
  cursor: pointer;
  break-inside: avoid;
  overflow: hidden;
  border-radius: 8px;
  transition: transform 0.3s ease;
}

.photo-card:hover {
  transform: scale(1.03);
}

.photo-card img {
  width: 100%;
  display: block;
  border-radius: 8px;
}

.photo-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 10px;
  background: var(--overlay-color);
  color: #fff;
  display: flex;
  flex-direction: column;
  gap: 4px;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.photo-card:hover .photo-overlay {
  opacity: 1;
}

.loading-placeholder {
  width: 100%;
  height: 200px;
  background: #ccc;
  border-radius: 8px;
  margin-bottom: 1rem;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% {
    opacity: 1;
  }

  50% {
    opacity: 0.5;
  }

  100% {
    opacity: 1;
  }
}

/* Responsive grid adjustments */
@media (max-width: 768px) {
  .grid-container {
    column-count: 2;
  }
}

@media (max-width: 480px) {
  .grid-container {
    column-count: 1;
  }
}
</style>
