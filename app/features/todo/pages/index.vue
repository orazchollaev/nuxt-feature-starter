<script setup lang="ts">
import type { TodoFilterItem, TodoFilterType } from "../types/todo.type"

useSeoMeta({
  title: "Nuxt 4 Feature-Based Starter",
  description: "Scalable Nuxt 4 architecture with feature-based structure and auto routing",
})

const store = useTodoStore()

const todos = computed(() => store.todos)
const filter = computed(() => store.filter)
const activeCount = computed(() => store.activeCount)
const completedCount = computed(() => store.completedCount)

const filters: TodoFilterItem[] = [
  { id: "all", text: () => $t("todo.all") },
  { id: "active", text: () => $t("todo.active") },
  { id: "completed", text: () => $t("todo.completed") },
]

const addTodo = (title: string) => store.addTodo(title)
const toggleTodo = (id: string) => store.toggleTodo(id)
const deleteTodo = (id: string) => store.deleteTodo(id)
const setFilter = (f: TodoFilterType) => store.setFilter(f)
</script>

<template>
  <div class="todo-page">
    <div class="page-header">
      <h1>{{ $t("todo.appName") }}</h1>
      <div class="filters">
        <button
          v-for="f in filters"
          :key="f.id"
          :class="{ active: filter === f.id }"
          class="filter-btn"
          @click="setFilter(f.id)"
        >
          {{ f.text() }}
        </button>
      </div>
    </div>

    <FTodoForm @submit="addTodo" />
    <FTodoList :todos="todos" @toggle="toggleTodo" @delete="deleteTodo" />

    <div v-if="todos.length > 0" class="stats">
      <p>
        <span class="stat-num">{{ activeCount }}</span>
        {{ $t("todo.active") }}
        &nbsp;·&nbsp;
        <span class="stat-num">{{ completedCount }}</span>
        {{ $t("todo.completed") }}
      </p>
    </div>
  </div>
</template>

<style scoped>
.todo-page {
  max-width: 760px;
  margin: 0 auto;
  padding: 2rem;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.75rem;
  flex-wrap: wrap;
  gap: 1rem;
}

h1 {
  font-family: var(--font-bang);
  font-size: 2.4rem;
  font-weight: 400;
  letter-spacing: 1px;
  color: var(--accent);
  text-shadow: 2px 2px 0 var(--ink);
  margin: 0;
}

.filters {
  display: flex;
  gap: 0.5rem;
}

.filter-btn {
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 0.85rem;
  letter-spacing: 0.5px;
  padding: 5px 14px;
  background: var(--bg-card);
  border: 3px solid var(--ink);
  border-radius: 3px;
  cursor: pointer;
  text-transform: uppercase;
  color: var(--text-muted);
  box-shadow: 3px 3px 0 var(--ink);
  transition: all 0.1s;
}

.filter-btn:hover {
  transform: translate(-1px, -1px);
  box-shadow: 4px 4px 0 var(--ink);
  border-color: var(--accent);
  color: var(--text-sub);
}

.filter-btn.active {
  background: var(--accent);
  color: var(--accent-text);
  border-color: var(--ink);
  transform: translate(-1px, -1px);
  box-shadow: 4px 4px 0 var(--ink);
}

.filter-btn:active {
  transform: translate(1px, 1px);
  box-shadow: 1px 1px 0 var(--ink);
}

.stats {
  margin-top: 1.25rem;
  padding: 0.75rem 1rem;
  background: var(--bg-card);
  border: 3px solid var(--ink);
  border-radius: 4px;
  text-align: center;
  color: var(--text-dim);
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 0.9rem;
  box-shadow: 4px 4px 0 var(--ink);
}

.stat-num {
  color: var(--accent);
  font-size: 1.2rem;
}
</style>
