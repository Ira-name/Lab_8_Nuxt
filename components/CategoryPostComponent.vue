<template>
  <div class="container mx-auto p-4">
    <UButton class="mb-4"
to="/BlogCategoryAdd"
label="Додати"
></UButton>
    <UInput v-model="q" placeholder="Фільтрувати категорії..." class="mb-4" />

    <div class="overflow-x-auto">
      <UTable v-model="selected" :rows="filteredCategories" :columns="columns" class="w-full whitespace-nowrap bg-white rounded-lg shadow-lg mb-4">
        <template #title-data="{ row }">
          <router-link :to="'/categories/${row.id}'">{{ row.title }}</router-link>
        </template>
        <template #parent-title-data="{ row }">
          <span>{{ row.parent_title ? row.parent_title : 'Без категорії' }}</span>
        </template>
        <template #actions-data="{ row }">
          <UDropdown :items="actionItems(row)">
            <UButton color="gray" variant="ghost" icon="i-heroicons-ellipsis-horizontal-20-solid" />
          </UDropdown>
        </template>
      </UTable>
    </div>

    <div class="flex justify-end">
      <UPagination v-model="page" :page-count="pageCount" :total="totalItems" />
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const columns = [
  { key: 'id', label: '#' },
  { key: 'title', label: 'Категорія' },
  { key: 'parent-title', label: 'Батьківська' },
  { key: 'actions', label: 'Дії' }
];

const page = ref(1);
const rowsPerPage = 7;
const q = ref('');
const totalItems = ref(0);
const categories = ref([]);
const selected = ref([]);

const fetchCategories = async () => {
  try {
    const response = await fetch('http://127.0.0.1:8000/api/categories');
    if (!response.ok) {
      throw new Error('Failed to fetch categories');
    }
    const data = await response.json();
    categories.value = data.map(category => ({
      ...category,
      parent_title: category.parent_title ? category.parent_title : null // Assuming parent_title is provided by Laravel API
    }));
    totalItems.value = categories.value.length;
  } catch (error) {
    console.error('Error fetching categories:', error);
  }
};

const filteredCategories = computed(() => {
  if (!q.value) {
    return categories.value.slice((page.value - 1) * rowsPerPage, page.value * rowsPerPage);
  }
  return categories.value.filter((category) => {
    return Object.values(category).some((value) => {
      return String(value).toLowerCase().includes(q.value.toLowerCase());
    });
  });
});

const goToAddCategoryPage = () => {
  // window.location.href = '/admin/blog/categories/create';
  window.location.href = '/api/categories/add';
 
};
//   const goToAddCategoryPage = () => {
//   router.push('api/categories/add');
// };

const editCategory = (id) => {
  // window.location.href =' /admin/blog/categories/edit/${id}';
  // <UButton class="mb-4" to="/BlogPostAdd" label="Додати"></UButton>
    window.location.href =' /BlogCategoryEdit';
};

const deleteCategory = async (id) => {
  try {
    const response = await fetch('http://127.0.0.1:8000/api/categories/${id}', {
      method: 'DELETE',
    });
    if (!response.ok) {
      throw new Error('Failed to delete category');
    }
    await fetchCategories();
  } catch (error) {
    console.error('Error deleting category:', error);
  }
};

const actionItems = (row) => [
  [
    {
      label: 'Редагувати',
      icon: 'i-heroicons-pencil-square-20-solid',
      click: () => editCategory(row.id)
      
    },
    {
      label: 'Видалити',
      icon: 'i-heroicons-trash-20-solid',
      click: () => deleteCategory(row.id)
    }
  ]
];

onMounted(fetchCategories);
</script>

<style scoped>
/* Additional styling can be added here if needed */
</style>