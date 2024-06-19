<template>
  <div class="w-1/3 m-auto">
    <div class="p-5">
    </div>
    <div class="p-5 border-2 rounded">
      <h1 class="text-center mb-2">Create post</h1>
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
          <UCheckbox v-model="state.is_published" :options="categoryOptions" />
        </UFormGroup>

        <UButton class="flex m-auto mt-5" type="submit">
          Create
        </UButton>
      </UForm>
    </div>
  </div>
</template>

<script setup lang="ts">
import { z } from 'zod';
import { reactive, ref, computed } from 'vue';
import axios from 'axios';

interface Category {
  id: number;
  title: string;
}
interface Post{
  title: string;
  slug: string;
  content_raw: string;
  excerpt: string;
  category_id: number;
  is_published: boolean;
}

const categories = ref<Category[]>([]);

const getCategories = async () => {
  try {
    const response = await $fetch<Category[]>(`http://127.0.0.1:8000/api/categories`);
    categories.value = response;
    console.log(categories.value);
  } catch (error) {
    console.error('Error fetching categories:', error);
  }
};
getCategories();

const posts = ref<Post[]>([]);
const getPosts = async () => {
  try {
    const response = await $fetch<Post[]>(`http://127.0.0.1:8000/api/blog/posts`);
    posts.value = response;
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};
getPosts();

// Computed property for category options
const categoryOptions = computed(() => {
  return categories.value.map(category => ({
    value: category.id.toString(), // Convert id to string
    label: category.title
  }));
});

// Define Zod schema for validation
const schema = z.object({
  title: z.string().min(5, 'Title must be at least 5 characters').max(200, 'Title must be less than 200 characters')
      .refine(value => {
        return !posts.value.some(post => post.title === value)
      }, 'Post title must be unique'),
  slug: z.string().max(200, 'Slug must be less than 200 characters').
  refine(value => {
    return !posts.value.some(post => post.slug === value)
  }, 'Post slug must be unique').optional(),
  content_raw: z.string().min(5, 'Description must be at least 5 characters').max(10000, 'Content must be less than 10000 characters'),
  category_id: z.string().refine(value => {
    const numberValue = Number(value);
    return Number.isInteger(numberValue) && categories.value.some(category => category.id === numberValue);
  }, 'Parent ID must be a valid integer and exist in categories'),
  is_published: z.boolean().optional(),
  excerpt: z.string().optional()
});

type Schema = z.infer<typeof schema>;

const state = reactive<Partial<Schema>>({
  title: undefined,
  slug: undefined,
  content_raw: undefined,
  category_id: undefined,
  is_published: undefined,
  excerpt: undefined
});

async function onSubmit (event: Event) {
  try {
    const result = schema.parse(state);
    console.log(result);

    // Convert parent_id to number before sending to server
    const dataToSend = {
      ...result,
      category_id: Number(result.category_id)
    };

    // Send the data to the server
    const response = await axios.post('http://127.0.0.1:8000/api/blog/posts', dataToSend);
    alert("Post successfully created!");
    window.location.href = '/posts'
  } catch (e) {
    if (e instanceof z.ZodError) {
      // Handle validation errors
      console.error(e.errors);
    } else {
      // Handle other errors
      console.error('Error submitting form:', e);
    }
  }
}
</script>
