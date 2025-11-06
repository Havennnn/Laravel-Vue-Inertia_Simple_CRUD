<template>
  <Head title="Tasks List" />

  <AuthenticatedLayout>
    <template #header>
      <div class="flex justify-between items-center">
        <h2 class="text-xl font-semibold leading-tight text-gray-800">Tasks List</h2>
        <Link :href="route('tasks.create')" class="inline-flex text-sm border px-2 py-1 font-bold text-white rounded-lg bg-gray-600 hover:bg-gray-700">
          + Add a Task
        </Link>
      </div>
    </template>

    <table class="w-full border border-gray-200 rounded-xl overflow-hidden">
      <thead class="bg-white border-b border-gray-200">
        <tr>
          <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Name</th>
          <th class="px-4 py-3 text-left text-sm font-medium text-gray-700">Description</th>
          <th class="px-4 py-3 text-center text-sm font-medium text-gray-700">Status</th>
          <th class="px-4 py-3 text-right text-sm font-medium text-gray-700">Actions</th>
        </tr>
      </thead>

      <tbody class="divide-y divide-gray-100">
        <tr
          v-for="task in tasks.data"
          :key="task.id"
          @click="goToShow(task.id)"
          class="hover:bg-gray-50/70 hover:cursor-pointer" prefetch
        >
          <td class="px-4 py-3 text-sm text-gray-900">{{ task.title }}</td>

          <td class="px-4 py-3 text-sm text-gray-600">
            <span class="line-clamp-2">{{ task.description }}</span>
          </td>

          <td class="px-4 py-3 text-center">
            <span
              class="inline-flex items-center rounded-full px-2.5 py-1 text-xs font-medium"
              :class="task.done
                ? 'bg-green-50 text-green-700 ring-1 ring-inset ring-green-200'
                : 'bg-amber-50 text-amber-700 ring-1 ring-inset ring-amber-200'"
            >
              {{ task.done ? 'Completed' : 'Pending' }}
            </span>
          </td>

          <td class="px-4 py-3" @click.stop>
            <div class="flex justify-end gap-2">
              <button
                @click.stop="goToEdit(task.id)"
                class="inline-flex items-center rounded-lg border border-gray-300 bg-white px-3 py-1.5 text-sm text-gray-700 hover:bg-gray-100 active:bg-gray-200"
              >
                Edit
              </button>
              <button
                @click.stop="handleDelete(task.id)"
                class="inline-flex items-center rounded-lg bg-red-600 px-3 py-1.5 text-sm text-white hover:bg-red-700 active:bg-red-800"
              >
                Delete
              </button>
            </div>
          </td>
        </tr>

        <tr v-if="!tasks.data || tasks.data.length === 0">
          <td colspan="4" class="px-6 py-10 text-center">
            <div class="mx-auto max-w-md">
              <div class="text-sm text-gray-500">No tasks found.</div>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Pagination (outside the table) -->
    <div v-if="tasks.links?.length" class="mt-4 flex flex-col sm:flex-row items-center justify-between gap-3 px-1">
      <div class="text-sm text-gray-600">
        Showing <span class="font-medium">{{ tasks.from ?? 0 }}</span>
        to <span class="font-medium">{{ tasks.to ?? 0 }}</span>
        of <span class="font-medium">{{ tasks.total ?? 0 }}</span> results
      </div>

      <nav class="isolate inline-flex -space-x-px rounded-md shadow-sm" aria-label="Pagination">
        <Link
          v-for="(link, index) in tasks.links"
          :key="index"
          :href="link.url || ''"
          preserve-scroll
          :only="['tasks']"
          v-html="link.label"
          class="px-3 py-2 text-sm border select-none"
          :class="[
            link.active
              ? 'z-10 bg-blue-600 text-white border-blue-600'
              : 'bg-white text-gray-700 hover:bg-gray-50 border-gray-300',
            !link.url ? 'pointer-events-none text-gray-400 bg-gray-50' : ''
          ]"
        />
      </nav>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue'
import { Head, Link, router } from '@inertiajs/vue3'

const props = defineProps({
  tasks: { type: Object, required: true } // paginator object
})

const goToShow = (id) => router.visit(route('tasks.show', id))
const goToEdit = (id) => router.visit(route('tasks.edit', id))

const handleDelete = (id) => {
  if (confirm('Are you sure you want to delete this task?')) {
    router.delete(route('tasks.destroy', id), { preserveScroll: true })
  }
}
</script>
