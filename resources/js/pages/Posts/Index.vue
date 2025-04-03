<script setup lang="ts">
import { Head, Link, router } from '@inertiajs/vue3'
import { ref, computed } from 'vue'
import AppLayout from '@/Layouts/AppLayout.vue'

// Props
const props = defineProps({
    posts: Object,
    filters: {
        type: Object,
        default: () => ({
            search: '',
            sort: 'created_at',
            direction: 'desc'
        })
    }
})

// Search functionality
const search = ref(props.filters.search || '')
const sort = ref(props.filters.sort || 'created_at')
const direction = ref(props.filters.direction || 'desc')

// Debounce search input
let timeout: NodeJS.Timeout
function updateSearch() {
    clearTimeout(timeout)
    timeout = setTimeout(() => {
        router.get('/posts', { search: search.value, sort: sort.value, direction: direction.value }, {
            preserveState: true,
            replace: true
        })
    }, 300)
}

// Sorting functionality
function updateSort(column: string) {
    if (sort.value === column) {
        direction.value = direction.value === 'asc' ? 'desc' : 'asc'
    } else {
        sort.value = column
        direction.value = 'asc'
    }

    router.get('/posts', { search: search.value, sort: sort.value, direction: direction.value }, {
        preserveState: true,
        replace: true
    })
}

// Toast notification (simplified)
function showToast(message: string) {
    alert(message) // Fallback to alert until you implement a proper toast system
}

// Breadcrumbs
const breadcrumbs = [
    { title: 'Posts', href: '/posts' },
]

// Content preview truncation
function truncate(text: string, length = 50) {
    return text && text.length > length ? text.substring(0, length) + '...' : text
}

// Delete confirmation with improved UX
interface Post {
    id: number
    title: string
    content: string
    created_at: string
}

function confirmDelete(post: Post) {
    if (confirm(`Are you sure you want to delete "${post.title}"?`)) {
        router.delete(route('posts.destroy', post.id), {
            onSuccess: () => {
                showToast('Post deleted successfully')
            }
        })
    }
}

// Computed properties for table styling
const getSortIndicator = (column: string) => {
    if (sort.value !== column) return null
    return direction.value === 'asc' ? '↑' : '↓'
}

const isEmpty = computed(() => props.posts.data.length === 0)
</script>

<template>
    <AppLayout :breadcrumbs="breadcrumbs">

        <Head title="Posts" />

        <div class="container mx-auto py-6 px-4 sm:px-6 lg:px-8">
            <!-- Header Section -->
            <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center mb-6 gap-4">
                <div>
                    <h1 class="text-2xl font-bold text-gray-900">Blog Posts</h1>
                    <p class="text-gray-600 mt-1">Manage your blog posts</p>
                </div>

                <Link href="/posts/create"
                    class="flex items-center bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors shadow-sm">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none" viewBox="0 0 24 24"
                    stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
                </svg>
                New Post
                </Link>
            </div>

            <!-- Search & Filters -->
            <div class="bg-white rounded-lg shadow-sm mb-6 p-4">
                <div class="flex flex-col sm:flex-row gap-4">
                    <div class="flex-grow">
                        <label for="search" class="sr-only">Search</label>
                        <div class="relative">
                            <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none">
                                <span class="text-gray-500">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none"
                                        viewBox="0 0 24 24" stroke="currentColor">
                                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                            d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z" />
                                    </svg>
                                </span>
                            </div>
                            <input id="search" v-model="search" @input="updateSearch" type="text"
                                placeholder="Search posts..."
                                class="p-3 block w-full rounded-md border-gray-300 pl-10 focus:border-blue-500 focus:ring-blue-500" />
                        </div>
                    </div>
                </div>
            </div>

            <!-- Posts Table -->
            <div class="bg-white rounded-lg shadow overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="min-w-full divide-y divide-gray-200">
                        <thead class="bg-gray-50">
                            <tr>
                                <th @click="updateSort('title')"
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider cursor-pointer hover:bg-gray-100">
                                    <div class="flex items-center space-x-1">
                                        <span>Title</span>
                                        <span v-if="getSortIndicator('title')" class="ml-1">{{ getSortIndicator('title')
                                            }}</span>
                                    </div>
                                </th>
                                <th
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    <div class="flex items-center space-x-1">
                                        <span>Content</span>
                                    </div>
                                </th>
                                <th @click="updateSort('created_at')"
                                    class="px-6 py-3 text-left text-xs font-medium text-gray-500 uppercase tracking-wider cursor-pointer hover:bg-gray-100">
                                    <div class="flex items-center space-x-1">
                                        <span>Created At</span>
                                        <span v-if="getSortIndicator('created_at')" class="ml-1">{{
                                            getSortIndicator('created_at') }}</span>
                                    </div>
                                </th>
                                <th
                                    class="px-6 py-3 text-right text-xs font-medium text-gray-500 uppercase tracking-wider">
                                    Actions
                                </th>
                            </tr>
                        </thead>
                        <tbody class="bg-white divide-y divide-gray-200">
                            <tr v-if="isEmpty" class="hover:bg-gray-50">
                                <td colspan="4" class="px-6 py-12 text-center text-gray-500">
                                    <div class="flex flex-col items-center justify-center">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-12 w-12 text-gray-400 mb-4"
                                            fill="none" viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                                        </svg>
                                        <p class="text-xl font-medium mb-2">No posts found</p>
                                        <p class="text-gray-500 mb-4">Get started by creating your first post.</p>
                                        <Link href="/posts/create"
                                            class="flex items-center bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition-colors shadow-sm">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-2" fill="none"
                                            viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M12 4v16m8-8H4" />
                                        </svg>
                                        Create New Post
                                        </Link>
                                    </div>
                                </td>
                            </tr>
                            <tr v-for="post in posts.data" :key="post.id" class="hover:bg-gray-50">
                                <td class="px-6 py-4 whitespace-nowrap text-sm font-medium text-gray-900">
                                    {{ post.title }}
                                </td>
                                <td class="px-6 py-4 text-sm text-gray-500 max-w-xs truncate">
                                    {{ truncate(post.content) }}
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap text-sm text-gray-500">
                                    {{ new Date(post.created_at).toLocaleDateString() }}
                                </td>
                                <td class="px-6 py-4 whitespace-nowrap text-right text-sm font-medium">
                                    <div class="flex justify-end space-x-2">
                                        <Link :href="`/posts/${post.id}`"
                                            class="text-blue-600 hover:text-blue-900 p-1 rounded hover:bg-blue-100">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none"
                                            viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                                        </svg>
                                        </Link>
                                        <Link :href="`/posts/${post.id}/edit`"
                                            class="text-green-600 hover:text-green-900 p-1 rounded hover:bg-green-100">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none"
                                            viewBox="0 0 24 24" stroke="currentColor">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z" />
                                        </svg>
                                        </Link>
                                        <button @click="confirmDelete(post)"
                                            class="text-red-600 hover:text-red-900 p-1 rounded hover:bg-red-100">
                                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none"
                                                viewBox="0 0 24 24" stroke="currentColor">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                                                    d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
                                            </svg>
                                        </button>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Pagination -->
                <div v-if="posts.links" class="bg-white px-4 py-3 flex items-center justify-between border-t border-gray-200 sm:px-6">
                    <div class="flex-1 flex justify-between sm:hidden">
                        <Link
                            :href="posts.links.prev"
                            :disabled="!posts.links.prev"
                            class="relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
                        >
                            Previous
                        </Link>
                        <Link
                            :href="posts.links.next"
                            :disabled="!posts.links.next"
                            class="ml-3 relative inline-flex items-center px-4 py-2 border border-gray-300 text-sm font-medium rounded-md text-gray-700 bg-white hover:bg-gray-50"
                        >
                            Next
                        </Link>
                    </div>
                    <div class="hidden sm:flex-1 sm:flex sm:items-center sm:justify-between">
                        <div>
                            <p class="text-sm text-gray-700">
                                Showing
                                <span class="font-medium">{{ posts.from }}</span>
                                to
                                <span class="font-medium">{{ posts.to }}</span>
                                of
                                <span class="font-medium">{{ posts.total }}</span>
                                results
                            </p>
                        </div>
                        <div>
                            <nav class="relative z-0 inline-flex rounded-md shadow-sm -space-x-px" aria-label="Pagination">
                                <template v-for="(link, index) in posts.links" :key="index">
                                    <Link
                                        v-if="link.url"
                                        :href="link.url"
                                        class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50"
                                        :class="{
                                            'z-10 bg-blue-50 border-blue-500 text-blue-600': link.active,
                                            'pointer-events-none opacity-50': !link.url
                                        }"
                                    >
                                        <span v-html="link.label" />
                                    </Link>
                                    <span
                                        v-else
                                        class="relative inline-flex items-center px-4 py-2 border border-gray-300 bg-white text-sm font-medium text-gray-500 cursor-not-allowed"
                                    >
                                        <span v-html="link.label" />
                                    </span>
                                </template>
                            </nav>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </AppLayout>
</template>
