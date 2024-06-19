<template>
  <div>
    <!-- <ProductList /> -->
    <nav>
      <router-link to="/BlogPostsUi" class="nav-link">
      <button class="btn-nav">BlogPostsUi</button>
    </router-link>
    <router-link to="/BlogCategory" class="nav-link">
      <button class="btn-nav">BlogCategory</button>
    </router-link>
    </nav>
    <NuxtPage />
  </div>
</template>

<script setup lang="ts">
// import BlogPostsUi from "~/pages/BlogPostsUi.vue";
// import BlogPost from "~/pages/BlogPost.vue";
// import ProductList from "~/pages/ProductList.vue";
</script>
<style scoped>
.btn-nav {
  display: inline-block;
  width: 100%;
  padding: 8px 16px; 
  font-size: 14px; 
  font-weight: bold;
  color: #edecf0; 
  background-color: #00580f; 
  border: 1px solid #00580f; 
  border-radius: 5px; 
  cursor: pointer;
  text-decoration: none;
  transition: background-color 0.3s ease; 
}

.btn-nav:hover {
  background-color: #109126; 
}
</style>