<template>
    <div class="container mx-auto p-4">
      <div v-if="post">
        <h1 class="text-2xl font-bold mb-4">{{ post.title }}</h1>
        <p class="mb-2">Автор: {{ post.user.name }}</p>
        <p class="mb-2">Категорія: {{ post.category.title }}</p>
        <p class="mb-4">{{ post.content }}</p>
        <p class="text-gray-500">Дата публікації: {{ post.published_at }}</p>
      </div>
      <div v-else>
        <p>Loading...</p>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, onMounted } from 'vue';
  import { useRoute } from 'vue-router';
  import { useFetch } from '#app'; 
  
  const route = useRoute();
  const post = ref(null);
  const fetchPost = async () => {
    const postId = route.params.id;
    const { data, error } = await useFetch(`http://127.0.0.1:8000/api/blog/posts/${postId}`);
    console.log('Data:', data);
    console.error('Error:', error.value);
    if (error.value) {
      console.error('Error fetching post:', error.value);
      return;
    }
    post.value = data.value;
  };
  
  onMounted(() => {
    fetchPost();
  });
  </script>
  
  <style scoped>

  </style>