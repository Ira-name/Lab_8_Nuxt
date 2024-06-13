<!-- <template>
  <div class="container mx-auto p-4">
    <div v-if="post" class="post-details">
      <h1 class="text-2xl font-bold mb-4">{{ post.title }}</h1>
      <p class="text-gray-700">{{ post.content }}</p>
      <p class="text-gray-500 text-sm">Автор: {{ post.user.name }}</p>
      <p class="text-gray-500 text-sm">Категорія: {{ post.category.title }}</p>
      <p class="text-gray-500 text-sm">Дата публікації: {{ post.published_at }}</p>
    </div>
    <div v-else>
      <p>Loading...</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { useRoute } from 'vue-router';
import { useFetch } from '#app';

const post = ref(null);
const route = useRoute();
const error = ref(null);

onMounted(async () => {
  const postId = route.params.id;
  const { data, error: fetchError } = await useFetch('http://127.0.0.1:8000/api/blog/posts/${postId}');
  
  if (fetchError.value) {
    console.error('Error fetching post:', fetchError.value);
    error.value = 'Failed to load post data';
    return;
  }

  if (data.value) {
    post.value = data.value;
  } else {
    error.value = 'Post not found';
  }
});
</script>

<style scoped>
/* Additional styling can be added here if needed */
</style> -->
<template>
  </template>