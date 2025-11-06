<template>
  <Head title="Edit Task" />
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

    <div class="flex items-center justify-center">
	    <form class="space-y-2 items-center justify-center w-1/4" @submit.prevent="handleUpdate">
		    <div>
	        <label class="block text-sm font-medium text-gray-700 mb-1">Title</label>
	        <input
	          type="text"
	          v-model="form.title"
	          class="w-full rounded-lg border-gray-300 focus:border-blue-500 focus:ring-blue-500"
	        />
	        <p v-if="form.errors.title" class="text-red-600 text-sm mt-1">{{ form.errors.title }}</p>
	      </div>

	      <!-- Description -->
	      <div>
	        <label class="block text-sm font-medium text-gray-700 mb-1">Description</label>
	        <textarea
	          rows="4"
	          v-model="form.description"
	          class="w-full rounded-lg border-gray-300 focus:border-blue-500 focus:ring-blue-500"
	        ></textarea>
	        <p v-if="form.errors.description" class="text-red-600 text-sm mt-1">{{ form.errors.description }}</p>
	      </div>

	      <!-- Done Status -->
	      <div>
	        <label class="block text-sm font-medium text-gray-700 mb-1">Status</label>
	        <select
	          v-model="form.done"
	          class="w-full rounded-lg border-gray-300 focus:border-blue-500 focus:ring-blue-500"
	        >
	          <option :value="0">Pending</option>
	          <option :value="1">Completed</option>
	        </select>
	        <p v-if="form.errors.done" class="text-red-600 text-sm mt-1">{{ form.errors.done }}</p>
	      </div>

	      <!-- Submit -->
	      <div class="flex justify-end">
	        <button
	          type="submit"
	          :disabled="form.processing"
	          class="mt-2 px-4 py-2 text-sm font-medium text-white bg-blue-600 rounded-lg hover:bg-blue-700 disabled:opacity-50"
	        >
	          Save Changes
	        </button>
	      </div>
		</form>
	</div>
  </AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link, useForm} from '@inertiajs/vue3';

const props = defineProps({
	task: { 
	  	type: Object,
		required: true
	}
})

const form = useForm({
	title: props.task.title,
	description: props.task.description,
	done: props.task.done ? 1 : 0,
})

const handleUpdate = () => {
	console.log(form.data());
	form.put(route('tasks.update', props.task.id));
}
</script>
