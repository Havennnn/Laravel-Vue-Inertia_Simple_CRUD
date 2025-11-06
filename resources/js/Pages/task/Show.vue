<template>
  <Head title="Show Task" />
  <AuthenticatedLayout>
    <template #header>
      <div class="flex items-center justify-between">
        <h2 class="text-xl font-semibold text-gray-800">Task Details</h2>
        <Link
          :href="route('tasks.index')"
          class="inline-flex items-center rounded-lg border border-gray-300 bg-white px-3 py-1.5 text-sm text-gray-700 hover:bg-gray-100"
        >
          ← Back
        </Link>
      </div>
    </template>

    <div>
      <div class="rounded-xl border border-gray-200 bg-white p-6">
        <div class="mb-4 flex items-start justify-between gap-4">
          <h1 class="text-lg font-semibold text-gray-900">{{ task.title }}</h1>
          <span
            class="inline-flex items-center rounded-full px-2.5 py-1 text-xs font-medium"
            :class="task.done
              ? 'bg-green-50 text-green-700 ring-1 ring-inset ring-green-200'
              : 'bg-amber-50 text-amber-700 ring-1 ring-inset ring-amber-200'"
          >
            {{ task.done ? 'Completed' : 'Pending' }}
          </span>
        </div>

        <div>
          <div class="text-xs uppercase tracking-wide text-gray-500">Description</div>
          <p class="mt-1 text-sm text-gray-700" v-if="task.description?.trim()">
            {{ task.description }}
          </p>
          <p class="mt-1 text-sm text-gray-400 italic" v-else>
            No description provided.
          </p>
        </div>
      </div>
    </div>
  </AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue'
import { Head, Link } from '@inertiajs/vue3'

const props = defineProps({
	task: { 
	  	type: Object,
		required: true
	}
})

</script>
