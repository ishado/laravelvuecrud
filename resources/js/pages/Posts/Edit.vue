<script setup lang="ts">
import { Head, Link, useForm, router } from '@inertiajs/vue3';
import { ref } from 'vue';
import AppLayout from '@/Layouts/AppLayout.vue';

// Props
const props = defineProps({
    post: { // The post object to edit, passed from the controller
        type: Object,
        required: true
    },
    errors: Object // Validation errors from backend
});

// Form state managed by Inertia's useForm, pre-filled with post data
const form = useForm({
    title: props.post.title,
    content: props.post.content
    // Add any other fields from your Post model that you want to edit
});

// Breadcrumbs
const breadcrumbs = [
    { title: 'Posts', href: route('posts.index') }, // Assumes route helper
    // Optional: Link to the specific post's show page if you have one
    // { title: props.post.title, href: route('posts.show', props.post.id) },
    { title: 'Edit', href: route('posts.edit', props.post.id) }
];

// Toast notification (simplified)
function showToast(message: string) {
    alert(message); // Fallback to alert
}

// Handle form submission for update
function submitForm() {
    // Use PUT or PATCH depending on your backend route definition (PUT is common for full resource update)
    form.put(route('posts.update', props.post.id), { // Route needs post ID
        onSuccess: () => {
            // showToast('Post updated successfully!');
            // Backend typically redirects, but you could force it:
            // router.visit(route('posts.index'));
        },
        onError: (errors) => {
            // Errors are automatically populated in the 'errors' prop
            console.error('Form submission errors:', errors);
            showToast('Failed to update post. Please check the errors.');
        }
    });
}

// Simple Cancel/Back functionality
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

        <Head :title="'Edit Post: ' + post.title" />

        <div class="container mx-auto py-6 px-4 sm:px-6 lg:px-8">
            <!-- Header Section -->
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 gap-4">
                <div>
                    <h1 class="text-2xl font-bold text-gray-900">Edit Post</h1>
                    <p class="text-gray-600 mt-1">Update the details for "{{ post.title }}"</p>
                </div>

                <button @click="goBack" type="button"
                    class="inline-flex items-center px-4 py-2 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none" viewBox="0 0 24 24"
                        stroke="currentColor" stroke-width="2">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
                    </svg>
                    Back
                </button>
            </div>

            <!-- Edit Form -->
            <div class="bg-white rounded-lg shadow-sm overflow-hidden">
                <form @submit.prevent="submitForm">
                    <div class="p-6 space-y-6">
                        <!-- Title Field -->
                        <div>
                            <label for="title" class="block text-sm font-medium text-gray-700">
                                Title
                            </label>
                            <div class="mt-1">
                                <input id="title" v-model="form.title" type="text" name="title" required
                                    :class="['p-3 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm', { 'border-red-500': errors?.title }]"
                                    placeholder="Enter post title">
                            </div>
                            <p v-if="errors?.title" class="mt-1 text-sm text-red-600">
                                {{ errors.title }}
                            </p>
                        </div>

                        <!-- Content Field -->
                        <div>
                            <label for="content" class="block text-sm font-medium text-gray-700">
                                Content
                            </label>
                            <div class="mt-1">
                                <textarea id="content" v-model="form.content" name="content" rows="6" required
                                    :class="['p-3 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-500 focus:ring-blue-500 sm:text-sm', { 'border-red-500': errors?.content }]"
                                    placeholder="Write your post content here..."></textarea>
                            </div>
                            <p v-if="errors?.content" class="mt-1 text-sm text-red-600">
                                {{ errors.content }}
                            </p>
                        </div>
                    </div>

                    <!-- Form Actions -->
                    <div class="bg-gray-50 px-4 py-3 sm:px-6 flex justify-end items-center space-x-3">
                        <button @click="goBack" type="button" :disabled="form.processing"
                            class="inline-flex justify-center py-2 px-4 border border-gray-300 rounded-md shadow-sm text-sm font-medium text-gray-700 bg-white hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 disabled:opacity-50">
                            Cancel
                        </button>
                        <button type="submit" :disabled="form.processing || !form.isDirty"
                            :class="['inline-flex justify-center py-2 px-4 border border-transparent rounded-md shadow-sm text-sm font-medium text-white focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-blue-500',
                                (form.processing || !form.isDirty) ? 'bg-blue-400 cursor-not-allowed opacity-75' : 'bg-blue-600 hover:bg-blue-700']">
                            <svg v-if="form.processing" class="animate-spin -ml-1 mr-3 h-5 w-5 text-white"
                                xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                                <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor"
                                    stroke-width="4"></circle>
                                <path class="opacity-75" fill="currentColor"
                                    d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z">
                                </path>
                            </svg>
                            {{ form.processing ? 'Saving...' : 'Update Post' }}
                        </button>
                    </div>
                </form>
            </div>
        </div>
    </AppLayout>
</template>

<style scoped>
/* Add any component-specific styles here if needed */
</style>
