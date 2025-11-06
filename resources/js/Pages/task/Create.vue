<template>
	<Head title="Create Task" />

	<AuthenticatedLayout>

		<template #header>
			<div class="flex justify-between items-center">
	            <h2
	                class="text-xl font-semibold leading-tight text-gray-800"
	            >
	                Create a task
	            </h2>
	            <Link :href="route('tasks.index')" class="inline-flex items-center rounded-lg border border-gray-300 bg-white px-3 py-1.5 text-sm text-gray-700 hover:bg-gray-100">← Return</Link>
	        </div>
        </template>

        <form class="items-center justify-center border" @submit.prevent="handleSubmit">
		    <label for="title">Title:</label>
		    <input name="title" id="title" type="text" v-model="form.title" />
		    <div v-if="form.errors.title">{{ form.errors.title }}</div>
		    
		    <label for="description">Description</label>
		    <textarea name="description" id="description" v-model="form.description" />
		    <div v-if="form.errors.description">{{ form.errors.description }}</div>

		    <button type="submit" :disabled="form.processing">Add</button>
		</form>
	</AuthenticatedLayout>
</template>

<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';

const form = useForm({
	title: '',
	description: '',
})

const handleSubmit = () => {
	console.log(form.data());
	form.post(route('tasks.store'));
}

</script>