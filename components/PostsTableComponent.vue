<template>

    <div class="container">

        <div class="flex justify-center">

            <div class="w-full">

                <nav class="navbar bg-gray-100">

                    <a href="/admin/blog/posts/create" class="">Додати</a>

                </nav>

                <div class="card">

                    <div class="card-body">

                        <table class="table table-auto">

                            <thead>

                            <tr>

                                <th>#</th>

                                <th>Автор</th>

                                <th>Категорія</th>

                                <th>Заголовок</th>

                                <th>Дата публікації</th>

                            </tr>

                            </thead>

                            <tbody>

                                <tr v-for="post in posts" :key="post.id">

                                    <td>{{ post.id }}</td>

                                    <td>{{ post.user.name }}</td>

                                    <td>{{ post.category.title }}</td>

                                    <td><a :href="'/admin/blog/posts/' + post.id + '/edit'">{{ post.title }}</a></td>

                                    <td>{{ post.published_at }}

                                    </td>
                                </tr>

                            </tbody>

                        </table>

                    </div>

                </div>

            </div>

        </div>

    </div>

</template>


<script setup lang="ts">
import { ref, onMounted } from 'vue';

const posts = ref([]); // Initialize posts as a ref with an empty array


const getPosts = () => {
    fetch('http://localhost:8000/api/blog/posts')
    .then(response => response.json()) 
    // Parse the response as JSON
    .then(data => {
        console.log(data);
        posts.value = data; // Update the posts array with fetched data
    })
    .catch(error => {
        console.error('Error fetching posts:', error);
    });
};

onMounted(getPosts); // Call getPosts function when the component is mounted
</script>
