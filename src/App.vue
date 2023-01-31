<!-- App is able to edit,delete
,sort list,
uses localstorage -->

<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const tasks = ref([])
const name = ref('')
const content = ref('')
const input_category = ref(null)
const tasks_asc = computed(() => tasks.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))
watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})
watch(tasks, (newVal) => {
	localStorage.setItem('tasks', JSON.stringify(newVal))
}, {
	deep: true
})
const addTodo = () => {
	if (content.value.trim() === '' || input_category.value === null) {
		return
	}
	tasks.value.push({
		content: content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}
const removeTodo = (task) => {
	tasks.value = tasks.value.filter((t) => t !== task)
}
onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
})
</script>

<template>
	<main class="app">
		
    <header>
      <h1>TO DO &#10004;</h1>
    </header>

		<section class="create-todo">
			<h3>CREATE A TODO</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. attend zoom meeting"
					v-model="content" />
				
				<h4>Select a category</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Work related</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Personal</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="task in tasks_asc" :class="`todo-item ${task.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="task.done" />
						<span :class="`bubble ${
							task.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>

					<div class="todo-content">
						<input type="text" v-model="task.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(task)">Delete</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>