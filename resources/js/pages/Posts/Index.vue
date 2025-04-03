<script setup lang="ts">
import { Head, Link } from '@inertiajs/vue3'
import AppLayout from '@/Layouts/AppLayout.vue'

// Props
const props = defineProps({
    posts: Array,
})

// Breadcrumbs
const breadcrumbs = [
    { title: 'Posts', href: '/posts' },
]

// Confirm delete handler
function confirmDelete(e) {
    if (!confirm('Are you sure?')) {
        e.preventDefault()
    }
}
</script>

<template>
    <AppLayout :breadcrumbs="breadcrumbs">

        <Head title="Posts" />
        <div class="container mx-auto p-4">
            <div class="flex justify-between items-center mb-4">
                <h1 class="text-2xl font-bold">Blog Posts</h1>
                <Link href="/posts/create" class="bg-gray-500 text-white px-4 py-1 rounded hover:bg-gray-600">
                Create
                </Link>
            </div>
            <div class="overflow-x-auto">
                <table class="min-w-full bg-white shadow rounded-lg">
                    <thead>
                        <tr class="bg-gray-200">
                            <th class="py-2 px-4 text-left border-b">Name</th>
                            <th class="py-2 px-4 text-left border-b">Content</th>
                            <th class="py-2 px-4 text-left border-b">Actions</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="post in posts" :key="post.id" class="hover:bg-gray-50">
                            <td class="py-2 px-4 border-b">{{ post.title }}</td>
                            <td class="py-2 px-4 border-b">{{ post.content }}</td>
                            <td class="py-2 px-4 border-b">
                                <Link :href="`/posts/${post.id}/edit`"
                                    class="bg-green-500 text-white px-2 py-1 rounded hover:bg-green-600 mr-2">
                                Edit
                                </Link>
                                <Link :href="route('posts.destroy', post.id)" method="delete"
                                    class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600"
                                    @click.prevent="confirmDelete($event)">
                                Delete
                                </Link>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </AppLayout>
</template>

