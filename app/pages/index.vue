<script setup lang="ts">
const { t } = useI18n()

interface Feature {
  icon: string
  titleKey: string
  descKey: string
}

const features: Feature[] = [
  {
    icon: "📦",
    titleKey: "home.features.featureBased.title",
    descKey: "home.features.featureBased.desc",
  },
  {
    icon: "🧩",
    titleKey: "home.features.componentPrefix.title",
    descKey: "home.features.componentPrefix.desc",
  },
  {
    icon: "🔥",
    titleKey: "home.features.hotReload.title",
    descKey: "home.features.hotReload.desc",
  },
  { icon: "🚀", titleKey: "home.features.typeSafe.title", descKey: "home.features.typeSafe.desc" },
  {
    icon: "📍",
    titleKey: "home.features.autoRouting.title",
    descKey: "home.features.autoRouting.desc",
  },
  {
    icon: "⚡",
    titleKey: "home.features.autoImports.title",
    descKey: "home.features.autoImports.desc",
  },
  { icon: "🌍", titleKey: "home.features.i18n.title", descKey: "home.features.i18n.desc" },
]

watchEffect(() => {
  useSeoMeta({
    title: t("home.hero.title"),
    description: t("home.hero.description"),
  })
})
</script>

<template>
  <div class="doc-page">
    <!-- HERO PANEL -->
    <div class="comic-panel hero-panel">
      <div class="panel-dots" />
      <span class="badge">template</span>
      <h1>{{ $t("home.hero.title") }}</h1>
      <p class="subtitle">{{ $t("home.hero.subtitle") }}</p>
      <div class="action-burst">★</div>
    </div>

    <!-- STRUCTURE PANEL -->
    <div class="comic-panel">
      <div class="panel-label">{{ $t("home.structure.title") }}</div>
      <p class="panel-body">{{ $t("home.structure.desc") }}</p>
      <pre class="comic-pre"><code><span class="hl">app/features/blog/</span>
  ├── <span class="hl">components/</span>   <span class="dim">→ &lt;FBlogCard /&gt;</span>
  ├── <span class="hl">composables/</span>  <span class="dim">→ useBlog()</span>
  ├── <span class="hl">stores/</span>       <span class="dim">→ useBlogStore()</span>
  ├── <span class="hl">types/</span>        <span class="dim">→ import type { Post }</span>
  ├── <span class="hl">pages/</span>        <span class="dim">→ /blog, /blog/create, /blog/:slug</span>
  └── <span class="hl">locales/</span>      <span class="dim">→ t('blog.title')</span></code></pre>
    </div>

    <!-- FEATURES PANEL -->
    <div class="comic-panel">
      <div class="panel-label">{{ $t("home.features.title") }}</div>
      <div class="feature-grid">
        <div v-for="feature in features" :key="feature.titleKey" class="feature-card">
          <span class="f-icon">{{ feature.icon }}</span>
          <div>
            <p class="f-title">{{ $t(feature.titleKey) }}</p>
            <p class="f-desc">{{ $t(feature.descKey) }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- USAGE PANEL -->
    <div class="comic-panel">
      <div class="panel-label">{{ $t("home.usage.title") }}</div>

      <p class="usage-label">{{ $t("home.usage.components") }}</p>
      <pre
        class="comic-pre"
      ><code><span class="hl">&lt;FTodoItem</span> :todo="todo" <span class="hl">/&gt;</span></code></pre>

      <p class="usage-label">{{ $t("home.usage.composables") }}</p>
      <pre
        class="comic-pre"
      ><code><span class="hl">const</span> store = <span class="hl">useTodoStore()</span>
<span class="hl">const</span> { todos } = <span class="hl">useTodo()</span></code></pre>

      <p class="usage-label">{{ $t("home.usage.i18n") }}</p>
      <pre
        class="comic-pre"
      ><code><span class="hl">t(</span><span class="dim">'todo.add'</span><span class="hl">)</span></code></pre>

      <p class="usage-label">{{ $t("home.usage.types") }}</p>
      <pre
        class="comic-pre"
      ><code><span class="hl">import type</span> { Todo } <span class="hl">from</span> <span class="dim">'~/features/todo/types/todo.types'</span></code></pre>
    </div>

    <!-- QUICK START PANEL -->
    <div class="comic-panel quickstart-panel">
      <div class="panel-label">{{ $t("home.quickStart.title") }}</div>
      <div class="cmd-block">
        <code>npm run create:feature blog</code>
      </div>
      <p class="hint">{{ $t("home.quickStart.hint") }}</p>
      <div class="pow-burst">POW!</div>
    </div>
  </div>
</template>

<style scoped>
.doc-page {
  max-width: 900px;
  margin: 0 auto;
  padding: 2.5rem 2rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

/* ── Comic panel base ── */
.comic-panel {
  background: var(--panel-bg);
  border: 3px solid var(--ink);
  border-radius: 4px;
  padding: 1.75rem 2rem;
  box-shadow: 6px 6px 0 var(--ink);
  position: relative;
  overflow: hidden;
}

/* halftone dots overlay on hero */
.panel-dots::before {
  content: "";
  position: absolute;
  inset: 0;
  background-image: radial-gradient(circle, var(--text-dim) 1.5px, transparent 1.5px);
  background-size: 14px 14px;
  opacity: 0.18;
  pointer-events: none;
}

/* ── Hero panel ── */
.hero-panel {
  background: var(--bg-card);
  border-width: 4px;
  box-shadow: 8px 8px 0 var(--ink);
}

.badge {
  display: inline-block;
  font-family: var(--font-bang);
  font-size: 13px;
  letter-spacing: 2px;
  text-transform: uppercase;
  padding: 3px 14px;
  border: 3px solid var(--accent);
  border-radius: 3px;
  background: transparent;
  color: var(--accent);
  box-shadow: 2px 2px 0 var(--accent);
  margin-bottom: 1rem;
}

h1 {
  font-family: var(--font-bang);
  font-size: clamp(2rem, 6vw, 3.5rem);
  font-weight: 400;
  line-height: 1.1;
  letter-spacing: 2px;
  margin-bottom: 0.875rem;
  color: var(--accent);
  text-shadow: 3px 3px 0 var(--ink);
}

.subtitle {
  font-size: clamp(14px, 2vw, 16px);
  color: var(--text-muted);
  line-height: 1.7;
  max-width: 560px;
  font-family: var(--font-body);
}

/* burst sticker */
.action-burst {
  position: absolute;
  top: 1.25rem;
  right: 1.5rem;
  font-size: 2.5rem;
  color: var(--yellow);
  text-shadow: 2px 2px 0 var(--ink);
  animation: spin-slow 8s linear infinite;
  pointer-events: none;
}

.pow-burst {
  position: absolute;
  bottom: 1rem;
  right: 1.5rem;
  font-family: var(--font-bang);
  font-size: 2rem;
  letter-spacing: 2px;
  color: var(--accent2);
  text-shadow: 2px 2px 0 var(--ink);
  transform: rotate(-8deg);
  pointer-events: none;
}

@keyframes spin-slow {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

/* ── Section labels ── */
.panel-label {
  font-family: var(--font-bang);
  font-size: 13px;
  letter-spacing: 2px;
  text-transform: uppercase;
  color: var(--accent);
  border-left: 4px solid var(--accent);
  padding-left: 10px;
  margin-bottom: 1rem;
}

.panel-body {
  font-size: 15px;
  color: var(--text-muted);
  line-height: 1.7;
  margin-bottom: 1rem;
  font-family: var(--font-body);
}

/* ── Code blocks ── */
.comic-pre {
  background: var(--bg-surface);
  border: 3px solid var(--ink);
  border-radius: 4px;
  padding: 1.1rem 1.4rem;
  font-size: 13.5px;
  line-height: 1.9;
  overflow-x: auto;
  margin: 0.5rem 0 1rem;
  box-shadow: 4px 4px 0 var(--ink);
}

.comic-pre code {
  font-family: "SF Mono", "Fira Code", monospace;
  background: none;
  color: var(--text-muted);
}

.hl {
  color: var(--accent);
}
.dim {
  color: var(--text-dim);
}

/* ── Feature grid ── */
.feature-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(230px, 1fr));
  gap: 10px;
  margin: 0.75rem 0;
}

.feature-card {
  display: flex;
  align-items: flex-start;
  gap: 12px;
  padding: 14px 16px;
  background: var(--bg-surface);
  border: 3px solid var(--ink);
  border-radius: 4px;
  box-shadow: 3px 3px 0 var(--ink);
  transition: all 0.1s;
}

.feature-card:hover {
  transform: translate(-2px, -2px);
  box-shadow: 5px 5px 0 var(--ink);
  border-color: var(--accent);
}

.f-icon {
  font-size: 22px;
  flex-shrink: 0;
  margin-top: 2px;
}

.f-title {
  font-family: var(--font-bang);
  font-size: 16px;
  letter-spacing: 0.5px;
  color: var(--text-sub);
  margin-bottom: 4px;
}

.f-desc {
  font-size: 13px;
  color: var(--text-muted);
  line-height: 1.55;
  font-family: var(--font-body);
}

/* ── Usage ── */
.usage-label {
  font-family: var(--font-bang);
  font-size: 13px;
  letter-spacing: 1px;
  color: var(--text-dim);
  margin: 1.1rem 0 0.3rem;
  text-transform: uppercase;
}

/* ── Quick start ── */
.quickstart-panel {
  background: var(--bg-card);
}

.cmd-block {
  display: inline-flex;
  align-items: center;
  background: var(--bg-surface);
  border: 3px solid var(--ink);
  border-radius: 4px;
  padding: 0.75rem 1.25rem;
  margin: 0.6rem 0;
  box-shadow: 4px 4px 0 var(--ink);
}

.cmd-block code {
  font-family: "SF Mono", "Fira Code", monospace;
  font-size: 15px;
  color: var(--accent);
}

.hint {
  font-size: 14px;
  color: var(--text-dim);
  margin-top: 0.6rem;
  line-height: 1.65;
  font-family: var(--font-body);
}

/* ── Mobile ── */
@media (max-width: 640px) {
  .doc-page {
    padding: 1.5rem 1rem;
    gap: 1rem;
  }
  .feature-grid {
    grid-template-columns: 1fr;
  }
  .comic-pre {
    font-size: 12px;
    padding: 0.875rem 1rem;
  }
  .cmd-block {
    width: 100%;
    justify-content: center;
  }
  h1 {
    text-shadow: 2px 2px 0 var(--ink);
  }
  .action-burst {
    display: none;
  }
}
</style>
