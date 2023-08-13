<template>
    <div class="app">
        <section class="greeting">
            <h2 class="title">
                Hey,
                <input
                    type="text"
                    id="name"
                    placeholder="Insert your name here"
                    v-model="name"
                />
            </h2>
        </section>

        <section class="create-todo">
            <h3>CREATE A TODO</h3>

            <form id="new-todo-form" @submit.prevent="addTodo">
                <h4>What's on your todo list?</h4>
                <input
                    type="text"
                    name="content"
                    id="content"
                    placeholder="e.g. make a video"
                    v-model="input_content"
                />

                <h4>Pick a category</h4>
                <div class="options">
                    <label>
                        <input
                            type="radio"
                            name="category"
                            id="category1"
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
                            id="category2"
                            value="personal"
                            v-model="input_category"
                        />
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
                <div
                    v-for="todo in todos_asc"
                    :class="`todo-item ${todo.done && 'done'}`"
                >
                    <label>
                        <input type="checkbox" v-model="todo.done" />
                        <span
                            :class="`bubble ${
                                todo.category == 'business'
                                    ? 'business'
                                    : 'personal'
                            }`"
                        ></span>
                    </label>

                    <div class="todo-content">
                        <input type="text" v-model="todo.content" />
                    </div>

                    <div class="actions">
                        <button class="delete" @click="removeTodo(todo)">
                            Delete
                        </button>
                    </div>
                </div>
            </div>
        </section>
    </div>
</template>

<script setup lang="ts">
import { ref, onMounted, computed, watch } from 'vue'

interface Todo {
    content: string
    category: 'business' | 'personal'
    done: boolean
    editable: boolean
    createdAt: number
}

const todos = ref<Todo[]>([])
const name = ref<string>('')

const input_content = ref<string>('')
const input_category = ref<'business' | 'personal' | null>(null)

const todos_asc = computed(() =>
    todos.value.slice().sort((a, b) => {
        return a.createdAt - b.createdAt
    })
)

watch(name, (newVal: string) => {
    localStorage.setItem('name', newVal)
})

watch(
    todos,
    (newVal: Todo[]) => {
        localStorage.setItem('todos', JSON.stringify(newVal))
    },
    {
        deep: true,
    }
)

const addTodo = () => {
    if (input_content.value.trim() === '' || input_category.value === null) {
        return
    }

    todos.value.push({
        content: input_content.value,
        category: input_category.value,
        done: false,
        editable: false,
        createdAt: new Date().getTime(),
    })
}

const removeTodo = (todo: Todo) => {
    todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
    name.value = localStorage.getItem('name') || ''

    const savedTodos = localStorage.getItem('todos')
    todos.value = savedTodos ? JSON.parse(savedTodos) : []
})
</script>

<style lang="scss"></style>
