<script setup>
import { ref, computed, onMounted, watch } from 'vue';
const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

// returns the todos created at ascending order
const todos_ascending = computed(() =>
    todos.value.sort((a, b) => {
        return b.createdAt - a.createdAt;
    }),
);

// storing the name in the local storage
watch(name, (newValue, oldValue) => {
    localStorage.setItem('name', newValue);
});

// pulling the name from the local storage
onMounted(name, (newValue, oldValue) => {
    name.value = localStorage.getItem('name') || '';
    todos.value = localStorage.getItem('todos') || [];
});

const addTodo = () => {
    if (input_content.value.trim() == '' || input_content.value == null) {
        return;
    }

    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        createdAt: new Date().getTime(),
    });
    input_content.value = '';
    input_category.value = null;
};

// storing the new todos in the local storage
watch(
    todos,
    (newValue, oldValue) => {
        localStorage.setItem('todos', JSON.stringify(newValue));
    },
    { deep: true },
);

// delete todo item
const removeTodo = (todo) => {
    todos.value = todos.value.filter((_todo) => _todo !== todo);
};
</script>

<template>
    <main class="app">
        <section class="greeting">
            <h2 class="title">
                What's up,
                <input type="text" placeholder="Name here" v-model="name" />
            </h2>
        </section>
        <section class="create-todo">
            <h3>CREATE A TODO</h3>

            <form @submit.prevent="addTodo">
                <h4>What's on your todo list ?</h4>
                <input type="text" placeholder="e.g make a video" v-model="input_content" />
                <h4>Pick a category</h4>
                <div class="options">
                    <label>
                        <input
                            type="radio"
                            name="category"
                            value="business"
                            v-model="input_category"
                        />
                        <span class="bubble business"></span>
                        <div>Business</div>
                    </label>
                    <label>
                        <input
                            type="radio"
                            name="category"
                            value="personal"
                            v-model="input_category"
                        />
                        <span class="bubble personal"></span>
                        <div>Personal</div>
                    </label>
                    {{ input_category }}
                </div>
                <input type="submit" value="Add Todo" />
            </form>
        </section>
        <section class="todo-list">
            <h3>Todo List</h3>
            <div class="list">
                <div v-for="todo in todos_ascending" :class="`todo-item ${todo.done && 'done'}`">
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span :class="`bubble ${todo.category}`"></span>
                    </label>
                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>
                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">Delete</button>
                    </div>
                </div>
            </div>
        </section>
    </main>
</template>
