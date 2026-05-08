<script setup lang="ts">
import type { Todo } from "../types/todo.type"

interface Props {
  todo: Todo
}
interface Emits {
  (e: "toggle" | "delete"): void
}

defineProps<Props>()
defineEmits<Emits>()
</script>

<template>
  <div class="todo-item" :class="{ completed: todo.completed }">
    <input type="checkbox" :checked="todo.completed" class="checkbox" @change="$emit('toggle')" />
    <span class="title">{{ todo.title }}</span>
    <button class="delete-btn" @click="$emit('delete')">
      {{ $t("todo.delete") }}
    </button>
  </div>
</template>

<style scoped>
.todo-item {
  display: flex;
  align-items: center;
  gap: 0.875rem;
  padding: 0.875rem 1.125rem;
  background: var(--bg-card);
  border: 3px solid var(--ink);
  border-radius: 4px;
  box-shadow: 4px 4px 0 var(--ink);
  transition: all 0.1s;
  position: relative;
}

.todo-item:hover {
  transform: translate(-2px, -2px);
  box-shadow: 6px 6px 0 var(--ink);
}

.todo-item.completed {
  opacity: 0.65;
  background: var(--bg-surface);
}

.todo-item.completed .title {
  text-decoration: line-through;
  color: var(--text-dim);
}

.checkbox {
  width: 1.15rem;
  height: 1.15rem;
  cursor: pointer;
  accent-color: var(--accent);
  border: 2px solid var(--ink);
  flex-shrink: 0;
}

.title {
  flex: 1;
  font-size: 0.95rem;
  font-family: var(--font-body);
  color: var(--text-sub);
  line-height: 1.4;
}

.delete-btn {
  padding: 0.3rem 0.875rem;
  background: transparent;
  color: var(--text-muted);
  border: 2px solid var(--border);
  border-radius: 3px;
  cursor: pointer;
  font-family: var(--font-bang);
  font-size: 0.85rem;
  letter-spacing: 0.5px;
  box-shadow: 2px 2px 0 var(--ink);
  transition: all 0.1s;
  text-transform: uppercase;
}

.delete-btn:hover {
  background: #ef4444;
  color: white;
  border-color: #ef4444;
  box-shadow: 3px 3px 0 var(--ink);
  transform: translate(-1px, -1px);
}

.delete-btn:active {
  transform: translate(1px, 1px);
  box-shadow: 0px 0px 0 var(--ink);
}
</style>
