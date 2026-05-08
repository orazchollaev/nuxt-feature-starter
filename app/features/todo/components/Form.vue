<script setup lang="ts">
interface Emits {
  (e: "submit", title: string): void
}

const emit = defineEmits<Emits>()
const title = ref("")

const handleSubmit = () => {
  if (title.value.trim()) {
    emit("submit", title.value.trim())
    title.value = ""
  }
}
</script>

<template>
  <form class="todo-form" @submit.prevent="handleSubmit">
    <input v-model="title" type="text" :placeholder="$t('todo.inputPlaceholder')" class="input" />
    <button type="submit" class="submit-btn">
      {{ $t("todo.add") }}
    </button>
  </form>
</template>

<style scoped>
.todo-form {
  display: flex;
  gap: 0.625rem;
  margin-bottom: 1.25rem;
}

.input {
  flex: 1;
  padding: 0.7rem 1rem;
  background: var(--bg-surface);
  border: 3px solid var(--ink);
  border-radius: 4px;
  font-size: 0.95rem;
  font-family: var(--font-body);
  color: var(--text-sub);
  box-shadow: 3px 3px 0 var(--ink);
  transition: all 0.1s;
}

.input::placeholder {
  color: var(--text-dim);
  font-family: var(--font-body);
}

.input:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 4px 4px 0 var(--accent);
  transform: translate(-1px, -1px);
}

.submit-btn {
  padding: 0.7rem 1.5rem;
  background: var(--accent);
  color: var(--accent-text);
  border: 3px solid var(--ink);
  border-radius: 4px;
  font-family: var(--font-bang);
  font-size: 1.1rem;
  letter-spacing: 1px;
  cursor: pointer;
  box-shadow: 4px 4px 0 var(--ink);
  transition: all 0.1s;
  text-transform: uppercase;
}

.submit-btn:hover {
  transform: translate(-2px, -2px);
  box-shadow: 6px 6px 0 var(--ink);
}

.submit-btn:active {
  transform: translate(1px, 1px);
  box-shadow: 1px 1px 0 var(--ink);
}
</style>
