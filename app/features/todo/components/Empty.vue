<template>
  <div class="todo-empty">
    <div class="empty-bubble">
      <p>No todos found. Create your first todo!</p>
    </div>
  </div>
</template>

<style scoped>
.todo-empty {
  padding: 2.5rem 1rem;
  display: flex;
  justify-content: center;
}

.empty-bubble {
  background: var(--bg-surface);
  border: 3px dashed var(--ink);
  border-radius: 4px;
  padding: 1.5rem 2.5rem;
  text-align: center;
  color: var(--text-dim);
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 1rem;
  box-shadow: 4px 4px 0 var(--ink);
  position: relative;
}

.empty-bubble::before {
  content: "¯\_(ツ)_/¯";
  display: block;
  font-size: 1.4rem;
  margin-bottom: 0.5rem;
  color: var(--text-muted);
  font-family: monospace;
}
</style>
