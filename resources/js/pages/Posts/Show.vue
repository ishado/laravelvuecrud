<script setup lang="ts">
import { Head, Link, router } from '@inertiajs/vue3';
import AppLayout from '@/Layouts/AppLayout.vue';

// Props
const props = defineProps({
    post: { // The post object to display
        type: Object,
        required: true
    },
    canEdit: { // Optional: if you want to conditionally show edit button
        type: Boolean,
        default: false
    }
});

// Breadcrumbs
const breadcrumbs = [
    { title: 'Posts', href: route('posts.index') },
    { title: props.post.title, href: route('posts.show', props.post.id) }
];

// Simple Back functionality
function goBack() {
    if (window.history.length > 1) {
        window.history.back();
    } else {
        router.visit(route('posts.index')); // Fallback to index
    }
}
</script>

<template>
    <AppLayout :breadcrumbs="breadcrumbs">

        <Head :title="post.title" />

        <div class="container mx-auto py-6 px-4 sm:px-6 lg:px-8">
            <!-- Header Section -->
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 gap-4">
                <div>
                    <h1 class="text-2xl font-bold text-gray-900">{{ post.title }}</h1>
                    <p class="text-gray-600 mt-1">
                        Posted on {{ new Date(post.created_at).toLocaleDateString() }}
                        <span v-if="post.updated_at !== post.created_at" class="text-gray-400 ml-2">
                            (Updated on {{ new Date(post.updated_at).toLocaleDateString() }})
                        </span>
                    </p>
                </div>

                <div class="flex space-x-3">
                    <button @click="goBack" type="button"
                        class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none" viewBox="0 0 24 24"
                            stroke="currentColor" stroke-width="2">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
                        </svg>
                        Back
                    </button>

                    <Link v-if="canEdit" :href="route('posts.edit', post.id)"
                        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-sm font-medium text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none" viewBox="0 0 24 24"
                        stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round"
                            d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                    </svg>
                    Edit
                    </Link>
                </div>
            </div>

            <!-- Post Content -->
            <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                <div class="p-6">
                    <div class="prose max-w-none">
                        <h2 class="text-xl font-semibold text-gray-800 mb-4">{{ post.title }}</h2>
                        <div class="text-gray-700 whitespace-pre-line">{{ post.content }}</div>
                    </div>
                </div>

                <!-- Optional Metadata Section -->
                <div class="bg-gray-50 px-4 py-3 sm:px-6 flex justify-between items-center">
                    <div class="text-sm text-gray-500">
                        <span v-if="post.author">By {{ post.author.name }}</span>
                    </div>
                    <div class="text-sm text-gray-500">
                        Last updated: {{ new Date(post.updated_at).toLocaleString() }}
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>

<style scoped>
.prose {
    line-height: 1.6;
}

.prose h2 {
    margin-bottom: 1rem;
}

.whitespace-pre-line {
    white-space: pre-line;
}
</style>
