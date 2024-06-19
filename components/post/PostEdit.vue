<template>
    <div class="w-1/3 m-auto">
      <div class="p-5">
       
      </div>
      <div class="p-5 border-2 rounded">
        <h1 class="text-center mb-2">Edit post</h1>
        <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
          <UFormGroup label="Title" name="title">
            <UInput v-model="state.title" />
          </UFormGroup>
  
          <UFormGroup label="Slug" name="slug">
            <UInput v-model="state.slug" />
          </UFormGroup>
  
          <UFormGroup label="Content" name="content">
            <UTextarea v-model="state.content_raw" />
          </UFormGroup>
  
          <UFormGroup label="Excerpt" name="excerpt">
            <UInput v-model="state.excerpt" />
          </UFormGroup>
  
          <UFormGroup label="Category" name="category_id">
            <USelect v-model="state.category_id" :options="categoryOptions" />
          </UFormGroup>
  
          <UFormGroup class="flex m-auto justify-center gap-3 items-center" label="Is published" name="is_published">
            <UCheckbox v-model="state.is_published" />
          </UFormGroup>
  
          <UButton class="flex m-auto mt-5" type="submit">
            Update
          </UButton>
        </UForm>
      </div>
    </div>
  </template>
  
  <script setup lang="ts">
  import { ref, reactive, computed, onMounted } from 'vue';
  import axios from 'axios';
  import { z } from 'zod';
  
  interface Category {
    id: number;
    title: string;
  }
  
  interface Post {
    id: number; // Ensure to include ID for editing
    title: string;
    slug: string;
    content_raw: string;
    excerpt: string;
    category_id: number;
    is_published: boolean;
  }
  
  const categories = ref<Category[]>([]);
  const posts = ref<Post[]>([]);
  const postId = ref<number | null>(null); // Track the ID of the post being edited
  
  const getCategories = async () => {
    try {
      const response = await axios.get<Category[]>('http://127.0.0.1:8000/api/categories');
      categories.value = response.data;
    } catch (error) {
      console.error('Error fetching categories:', error);
    }
  };
  
  const getPostById = async (id: number) => {
    try {
      const response = await axios.get<Post>(`http://127.0.0.1:8000/api/blog/posts/${id}`);
      const post = response.data;
      postId.value = post.id; // Set the post ID being edited
      state.title = post.title;
      state.slug = post.slug;
      state.content_raw = post.content_raw;
      state.excerpt = post.excerpt;
      state.category_id = post.category_id.toString(); // Assuming category_id is stored as string
      state.is_published = post.is_published;
    } catch (error) {
      console.error('Error fetching post:', error);
    }
  };
  
  // Computed property for category options
  const categoryOptions = computed(() => {
    return categories.value.map(category => ({
      value: category.id.toString(),
      label: category.title
    }));
  });
  
  // Define Zod schema for validation
  const schema = z.object({
    title: z.string().min(5, 'Title must be at least 5 characters').max(200, 'Title must be less than 200 characters'),
    slug: z.string().max(200, 'Slug must be less than 200 characters'),
    content_raw: z.string().min(5, 'Description must be at least 5 characters').max(10000, 'Content must be less than 10000 characters'),
    category_id: z.string().refine(value => {
      const numberValue = Number(value);
      return Number.isInteger(numberValue) && categories.value.some(category => category.id === numberValue);
    }, 'Category ID must be a valid integer and exist in categories'),
    is_published: z.boolean()
  });
  
  type Schema = z.infer<typeof schema>;
  
  const state = reactive<Partial<Schema>>({
    title: '',
    slug: '',
    content_raw: '',
    category_id: '',
    is_published: false,
    excerpt: ''
  });
  
  // Function to handle form submission
  async function onSubmit(event: Event) {
    event.preventDefault();
    try {
      const result = schema.parse(state);
  
      // Convert category_id to number before sending to server
      const dataToSend: Post = {
        ...result,
        id: postId.value!, // Ensure postId has a value here
        category_id: Number(result.category_id)
      };
  
      // Perform update request
      await axios.put(`http://127.0.0.1:8000/api/blog/posts/${postId.value}`, dataToSend);
      alert('Post updated successfully!');
      window.location.href = '/posts'; // Redirect to posts page after action
    } catch (error) {
      if (error instanceof z.ZodError) {
        console.error('Validation error:', error.errors);
      } else {
        console.error('API error:', error);
      }
    }
  }
  
  // Fetch categories and specific post data on component mount
  onMounted(() => {
    getCategories();
    // Replace with the actual post ID you want to edit
    const postIdToEdit = 1; // Replace with the actual post ID
    getPostById(postIdToEdit);
  });
  </script>