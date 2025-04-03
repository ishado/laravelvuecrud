<script setup>
    import { Head, Link, useForm } from '@inertiajs/vue3'
    import AppLayout from '@/Layouts/AppLayout.vue'

    // Breadcrumbs
    const breadcrumbs = [
        { title: 'Posts', href: '/posts' },
    ]

    // Setup form
    const form = useForm({
        title: '',
        content: '',
    })

    // Submit handler
    function submit() {
        form.post(route('posts.store'), {
            preserveScroll: true,
            onSuccess: () => form.reset(),
        })
    }
</script>

<template>
    <AppLayout :breadcrumbs="breadcrumbs">

        <Head title="Create Post" />
        <div class="container mx-auto p-4">
            <div class="flex justify-between items-center mb-4">
                <h1 class="text-2xl font-bold">Blog Posts</h1>
            </div>
            <div class="bg-white shadow rounded-lg p-4 mb-6">
                <h2 class="text-xl font-bold mb-4">Add New Post</h2>
                <form @submit.prevent="submit">
                    <div class="mb-4">
                        <label for="title" class="block text-gray-700 font-medium mb-1">Title</label>
                        <input type="text" id="title" v-model="form.title" placeholder="Post Title"
                            class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring focus:ring-blue-200" />
                        <div v-if="form.errors.title" class="text-red-500 text-sm mt-1">
                            {{ form.errors.title }}
                        </div>
                    </div>
                    <div class="mb-4">
                        <label for="content" class="block text-gray-700 font-medium mb-1">Content</label>
                        <textarea id="content" v-model="form.content" placeholder="Post Content" rows="4"
                            class="w-full border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring focus:ring-blue-200"></textarea>
                        <div v-if="form.errors.content" class="text-red-500 text-sm mt-1">
                            {{ form.errors.content }}
                        </div>
                    </div>
                    <button type="submit" :disabled="form.processing"
                        class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
                        {{ form.processing ? 'Saving...' : 'Save' }}
                    </button>
                </form>
            </div>
        </div>
    </AppLayout>
</template>


