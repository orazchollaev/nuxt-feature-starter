<script setup lang="ts">
const { locale, locales, setLocale } = useI18n()
const colorMode = useColorMode()

const toggleLocale = async () => {
  const available = locales.value.map((l) => (typeof l === "string" ? l : l.code))
  const next = available.find((l) => l !== locale.value)
  if (next) await setLocale(next)
}

const toggleColorMode = () => {
  colorMode.preference = colorMode.value === "dark" ? "light" : "dark"
}
</script>

<template>
  <header class="comic-header">
    <div class="container">
      <NuxtLink to="https://github.com/orazchollaev" target="_blank" class="logo-text">
        NUXT FEATURE STARTER
      </NuxtLink>

      <nav class="nav-area">
        <NuxtLink to="/" class="nav-link" title="Shift + H">
          {{ $t("layout.nav.home") }}
        </NuxtLink>
        <NuxtLink to="/todo" class="nav-link" title="Shift + T">
          {{ $t("layout.nav.todo") }}
        </NuxtLink>
        <div class="controls">
          <button
            class="toggle-btn"
            :title="colorMode.value === 'dark' ? 'Light mode' : 'Dark mode'"
            @click="toggleColorMode"
          >
            <IconDarkMode v-if="colorMode.value === 'dark'" />
            <IconLightMode v-else />
          </button>
          <button class="toggle-btn" @click="toggleLocale">
            {{ locale.toUpperCase() }}
          </button>
        </div>
      </nav>
    </div>
  </header>
</template>

<style scoped>
.comic-header {
  background: var(--bg);
  border-bottom: 4px solid var(--ink);
  position: sticky;
  top: 0;
  z-index: 100;
  box-shadow: 0 4px 0 var(--ink);
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0.875rem 2rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.logo-text {
  font-family: var(--font-bang);
  font-size: 1.6rem;
  letter-spacing: 2px;
  color: var(--accent);
  text-shadow: 3px 3px 0 var(--ink);
  text-decoration: none;
  transition: all 0.1s;
}

.logo-text:hover {
  color: var(--accent2);
  text-shadow:
    3px 3px 0 var(--ink),
    -1px -1px 0 var(--ink);
}

.nav-area {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.nav-link {
  font-family: var(--font-bang);
  font-size: 1.15rem;
  letter-spacing: 1px;
  color: var(--ink);
  text-decoration: none;
  border: 3px solid var(--ink);
  padding: 4px 16px;
  background: var(--yellow);
  box-shadow: 3px 3px 0 var(--ink);
  transition: all 0.1s;
  display: inline-block;
  text-transform: uppercase;
  color: #1a1a2e;
}

.nav-link:hover,
.nav-link.router-link-active {
  background: var(--accent);
  color: #0f0f0f;
  transform: translate(-2px, -2px);
  box-shadow: 5px 5px 0 var(--ink);
}

.controls {
  display: flex;
  gap: 0.5rem;
}

.toggle-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: var(--font-bang);
  font-size: 0.95rem;
  letter-spacing: 1px;
  border: 3px solid var(--ink);
  padding: 4px 14px;
  background: var(--bg-card);
  color: var(--ink);
  box-shadow: 3px 3px 0 var(--ink);
  cursor: pointer;
  transition: all 0.1s;
  min-height: 36px;
}

.toggle-btn:hover {
  transform: translate(-2px, -2px);
  box-shadow: 5px 5px 0 var(--ink);
  background: var(--accent2);
  color: #fff;
}

.toggle-btn:active {
  transform: translate(1px, 1px);
  box-shadow: 1px 1px 0 var(--ink);
}
</style>
