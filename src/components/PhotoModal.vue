<template>
    <div class="modal-overlay" @click.self="close">
      <div class="modal-content">
        <button class="close-btn" @click="close">&times;</button>
        <img :src="photo.urls.regular" :alt="photo.alt_description || 'Photo'" />
        <div class="modal-details">
          <p class="author">{{ photo.user.name }}</p>
          <p class="location">
            {{ photo.location && photo.location.name ? photo.location.name : "Unknown Location" }}
          </p>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>

  

  
  const close = () => {
    emit("close");
  };
  </script>
  
  <style scoped>
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.7);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 100;
    animation: fadeIn 0.3s ease;
  }
  
  @keyframes fadeIn {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  
  .modal-content {
    position: relative;
    max-width: 90%;
    max-height: 90%;
    background: #fff;
    border-radius: 8px;
    overflow: hidden;
    animation: slideIn 0.3s ease;
  }
  
  @keyframes slideIn {
    from {
      transform: translateY(-20px);
      opacity: 0;
    }
    to {
      transform: translateY(0);
      opacity: 1;
    }
  }
  
  .modal-content img {
    width: 100%;
    height: auto;
    display: block;
  }
  
  .close-btn {
    position: absolute;
    top: 10px;
    right: 10px;
    background: transparent;
    border: none;
    font-size: 2rem;
    cursor: pointer;
    color: #fff;
    z-index: 10;
  }
  
  .modal-details {
    padding: 10px;
    background: #f8f8f8;
    display: flex;
    justify-content: space-between;
    border-top: 1px solid #ddd;
  }
  </style>
  