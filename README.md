# ✅ Task CRUD — Laravel + Inertia.js + Vue 3

This project is a simple **Task Management** CRUD application built with:

- **Laravel (Backend + API + Database)**
- **Inertia.js (SPA Routing without API calls)**
- **Vue 3 (Frontend UI)**

It demonstrates a very simple create, read, update, and delete tasks, while still leveraging Laravel’s server-side routing and validation.

---

## 📂 Features

| Feature                   | Description                               |
|---------------------------|-------------------------------------------|
| List Tasks                | Displays user-owned tasks with pagination |
| Show Task Details         | View full task details                    |
| Create Task               | Form to add a new task                    |
| Edit Task                 | Update fields including status            |
| Delete Task               | Remove task with confirmation             |
| Flash Toast Notifications | Shows success messages after actions      |
| Authorization             | Each user sees only their own tasks       |

---

## 🛠 Routes Overview

```php
Route::middleware(['auth', 'verified'])->group(function () {
    Route::get('/tasks', [TaskController::class, 'index'])->name('tasks.index');
    Route::get('/tasks/create', [TaskController::class, 'create'])->name('tasks.create');
    Route::post('/tasks', [TaskController::class, 'store'])->name('tasks.store');
    Route::get('/tasks/{task}', [TaskController::class, 'show'])->name('tasks.show');
    Route::get('/tasks/{task}/edit', [TaskController::class, 'edit'])->name('tasks.edit');
    Route::put('/tasks/{task}', [TaskController::class, 'update'])->name('tasks.update');
    Route::delete('/tasks/{task}', [TaskController::class, 'destroy'])->name('tasks.destroy');
});
```

---

## 🧠 Controller and Frontend Logic Logic

### List Tasks (`index`)
```php
public function index(Request $request)
{
    $tasks = $request->user()
        ->tasks()
        ->latest()
        ->paginate(15);

    return Inertia::render('task/Index', compact('tasks'));
}
```

```html
<script setup>
const props = defineProps({
  tasks: { type: Object, required: true }
})
</script>

<template>
    <tr v-for="task in tasks.data" :key="task.id"">

      <td>{{ task.title }}</td>
      <td>{{ task.description }}</td>
      <td>{{ task.done ? 'Completed' : 'Pending' }}</td>

      <td>
        <div>
          <button>Edit</button>
          <button>Delete</button>
        </div>
      </td>

    </tr>
</template>
```

### Show Task (`show`)
```php
public function show(Task $task)
{
    return Inertia::render('task/Show', compact('task'));
}
```

```html
<script setup>
const props = defineProps({
    task: { 
        type: Object,
        required: true
    }
})
</script>

<template>
    <div>
      <h1>{{ task.title }}</h1>
      <span>{{ task.done ? 'Completed' : 'Pending' }}</span>
    </div>

    <div>
      <h2>Description</h2>
      <p v-if="task.description?">{{ task.description }}</p>
      <p v-else>No description provided.</p>
    </div>
</template>
```

### Create Task (`create`)
```php
public function show(Task $task)
{
    return Inertia::render('task/Show', compact('task'));
}
```

```vue
<Link :href="route('tasks.create')"> + Add a Task </Link>
````

### Store Task(`store`)
```php
public function store(Request $request)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'description' => 'required|string|max:2000',
    ]);

    $request->user()->tasks()->create($validated);

    return redirect()->route('tasks.index')
        ->with('message', 'Task added successfully')
        ->with('type', 'success');
}
```

```html
<script setup>
import { useForm } from '@inertiajs/vue3';

const form = useForm({
    title: '',
    description: '',
})

const handleSubmit = () => {
    console.log(form.data());
    form.post(route('tasks.store'));
}
</script>

<template>
    <form @submit.prevent="handleSubmit">
        <label for="title">Title:</label>
        <input id="title" type="text" v-model="form.title" />
        <div v-if="form.errors.title">{{ form.errors.title }}</div>
        
        <label for="description">Description</label>
        <textarea id="description" v-model="form.description" />
        <div v-if="form.errors.description">{{ form.errors.description }}</div>

        <button type="submit" :disabled="form.processing">Add</button>
    </form>
</template>
```

### Edit Task (`edit`)
```php
public function edit(Task $task)
{
    return Inertia::render('task/Edit', compact('task'));
}
```

```html
<script setup>
const props = defineProps({
  tasks: { type: Object, required: true }
})

const goToShow = (id) => router.visit(route('tasks.show', id))
const goToEdit = (id) => router.visit(route('tasks.edit', id))
</script>

<template>
    <tr v-for="task in tasks.data" :key="task.id" @click="goToShow(task.id)">

      ... index tables and rows

      <td click.stop>
        <div>
          <button @click.stop="goToEdit(task.id)">Edit</button>
          <button>Delete</button>
        </div>
      </td>

    </tr>
<template>
```

### Update Task (`update`)
```php
public function update(Request $request, Task $task)
{
    $validated = $request->validate([
        'title' => 'required|string|max:255',
        'description' => 'required|string|max:2000',
        'done' => 'required|boolean',
    ]);

    $task->update($validated);

    return redirect()->route('tasks.index')
        ->with('message', 'Task updated successfully')
        ->with('type', 'success');
}
```

```html
<script setup>
import { useForm } from '@inertiajs/vue3';

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

<template
    <form@submit.prevent="handleUpdate">
      <div>
        <label for="title">Title</label>
        <input id="title" type="text" v-model="form.title" />
        <p v-if="form.errors.title">{{ form.errors.title }}</p>
      </div>

      <div>
        <label for="description">Description</label>
        <textarea v-model="form.description" />
        <p v-if="form.errors.description">{{ form.errors.description }}</p>
      </div>

      <div>
        <label for='status'>Status</label>
        <select v-model="form.done">
          <option :value="0">Pending</option>
          <option :value="1">Completed</option>
        </select>
        <p v-if="form.errors.done">{{ form.errors.done }}</p>
      </div>

      <div>
        <button type="submit" :disabled="form.processing" >
          Save Changes
        </button>
      </div>
    </form>
</template>
```

### Delete (`destroy`)
```php
public function destroy(Task $task)
{
    $task->delete();

    return redirect()->route('tasks.index')
        ->with('message', 'Task deleted successfully')
        ->with('type', 'success');
}
```

```html
<script setup>
const props = defineProps({
  tasks: { type: Object, required: true }
})

const goToShow = (id) => router.visit(route('tasks.show', id))
const goToEdit = (id) => router.visit(route('tasks.edit', id))

const handleDelete = (id) => {
  if (confirm('Are you sure you want to delete this task?')) {
    router.delete(route('tasks.destroy', id), { preserveScroll: true })
  }
}

</script>

<template>
    <tr v-for="task in tasks.data" :key="task.id" @click="goToShow(task.id)">

      ... index tables and rows

      <td @click.stop>
        <div>
          <button @click.stop="goToEdit(task.id)">Edit</button>
          <button @click.stop="handleDelete(task.id)">Delete</button>
        </div>
      </td>

    </tr>
<template>
```

## 🎨 Vue 3 Frontend Logic

### ✅ Pagination (`task/Index.vue`)

```html
<div v-if="tasks.links?.length">
  <div>
    Showing {{ tasks.from ?? 0 }} to {{ tasks.to ?? 0 }} of {{ tasks.total ?? 0 }} results
  </div>

  <nav aria-label="Pagination">
    <Link
      v-for="(link, index) in tasks.links"
      :key="index"
      :href="link.url || ''"
      preserve-scroll
      :only="['tasks']"
      v-html="link.label"
      :class="[link.active, !link.url]"
    />
  </nav>
</div>
```

## 🔔 Toast Notifications (Global in Layout)

Triggered on any Inertia redirect:

```js
import { usePage } from '@inertiajs/vue3';

const page = usePage()
const toast = ref(null)
const toastType = ref('success')

let hideTimer = null

function showToast(message, type = 'success', ms = 2000) {
  toast.value = message
  toastType.value = type

  if (hideTimer) clearTimeout(hideTimer)
  hideTimer = window.setTimeout(() => (toast.value = null), ms)
}

function handleFlash() {
  const msg = page.props.flash?.message
  const type = page.props.flash?.type || 'success'
  if (msg) showToast(msg, type)
}

onMounted(() => {
  handleFlash()
  router.on('success', handleFlash)
})

onBeforeUnmount(() => {
  if (hideTimer) clearTimeout(hideTimer)
})
```

```html
<div v-if="toast" @click="toast = null">
    {{ toast }}
</div>
```

---

## ✅ Summary

| Action   | View          | Route                 | Method               |
|----------|---------------|-----------------------|----------------------|
| List     | `Index.vue`   | GET `/tasks`          | index                |
| Show     | `Show.vue`    | GET `/tasks/{task}`   | show                 |
| Create   | `Create.vue`  | POST `/tasks`         | store                |
| Edit     | `Edit.vue`    | PUT `/tasks/{task}`   | update               |
| Delete   | `Index.vue`   | DELETE `/tasks/{task}`| destroy              |
| Toasts   | Global Layout | Flash messages        | router.on('success') |
