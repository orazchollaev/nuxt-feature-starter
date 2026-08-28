<script setup lang="ts">
import type { NuxtError } from "#app"

const props = defineProps<{ error: NuxtError }>()

const { t } = useI18n()
const router = useRouter()

const statusCode = computed(() => props.error?.status ?? props.error?.statusCode ?? 500)
const isNotFound = computed(() => statusCode.value === 404)

// Error messages can carry internals, so only surface them during development.
const devMessage = computed(() => (import.meta.dev ? props.error?.message : null))

const title = computed(() => (isNotFound.value ? t("error.notFound") : t("error.error")))
const description = computed(() =>
  isNotFound.value ? t("error.notFoundDesc") : t("error.errorDesc")
)

// Router history keeps the previous entry, so we can redirect there instead of
// calling router.back() — the error page has to be cleared before navigating.
const previousPath = computed(() => {
  const back = router.options.history.state?.back
  return typeof back === "string" && back !== router.currentRoute.value.fullPath ? back : null
})

const goHome = () => clearError({ redirect: "/" })
const goBack = () => clearError({ redirect: previousPath.value ?? "/" })

watchEffect(() => {
  useSeoMeta({
    title: `${statusCode.value} — ${title.value}`,
    description: description.value,
  })
})
</script>

<template>
  <NuxtLayout>
    <main>
      <div class="error-page">
        <div class="comic-panel">
          <div class="panel-dots" />

          <div>
            <span class="badge">{{ isNotFound ? "lost" : "oops" }}</span>
          </div>

          <div class="code-wrap">
            <p class="error-code">{{ statusCode }}</p>
            <div class="oops-burst">{{ isNotFound ? "WHOOPS!" : "BAM!" }}</div>
          </div>

          <h1 class="error-title">{{ title }}</h1>
          <p class="error-desc">{{ description }}</p>

          <pre v-if="devMessage" class="comic-pre"><code>{{ devMessage }}</code></pre>

          <div class="actions">
            <button class="btn btn-primary" @click="goHome">
              {{ $t("error.backHome") }}
            </button>
            <button v-if="previousPath" class="btn btn-ghost" @click="goBack">
              {{ $t("error.goBack") }}
            </button>
          </div>
        </div>

        <div class="help-bubble">
          <p>
            {{ $t("error.needHelp") }}
            <NuxtLink
              to="https://github.com/orazchollaev/nuxt-feature-starter/issues"
              target="_blank"
              class="help-link"
            >
              {{ $t("error.contactSupport") }}
            </NuxtLink>
          </p>
        </div>
      </div>
    </main>
  </NuxtLayout>
</template>

<style scoped>
.error-page {
  max-width: 640px;
  margin: 0 auto;
  padding: 2.5rem 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
  text-align: center;
}

/* ── Comic panel base ── */
.comic-panel {
  width: 100%;
  background: var(--bg-card);
  border: 4px solid var(--ink);
  border-radius: 4px;
  padding: 2.5rem 2rem 2rem;
  box-shadow: 8px 8px 0 var(--ink);
  position: relative;
  overflow: hidden;
}

/* halftone dots overlay */
.panel-dots::before {
  content: "";
  position: absolute;
  inset: 0;
  background-image: radial-gradient(circle, var(--text-dim) 1.5px, transparent 1.5px);
  background-size: 14px 14px;
  opacity: 0.18;
  pointer-events: none;
}

.badge {
  display: inline-block;
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 12px;
  letter-spacing: 1px;
  text-transform: uppercase;
  padding: 3px 14px;
  border: 3px solid var(--accent);
  border-radius: 3px;
  color: var(--accent);
  box-shadow: 2px 2px 0 var(--accent);
  margin-bottom: 0.75rem;
}

/* ── Status code ── */
.code-wrap {
  position: relative;
  display: inline-block;
}

.error-code {
  font-family: var(--font-bang);
  font-size: clamp(5rem, 22vw, 9rem);
  line-height: 1;
  letter-spacing: 2px;
  color: var(--accent);
  text-shadow:
    4px 4px 0 var(--accent2),
    8px 8px 0 var(--ink);
  animation: shake 6s ease-in-out infinite;
}

.oops-burst {
  position: absolute;
  top: -0.25rem;
  right: -3.25rem;
  font-family: var(--font-bang);
  font-size: 1.5rem;
  letter-spacing: 2px;
  color: var(--yellow);
  text-shadow: 2px 2px 0 var(--ink);
  transform: rotate(12deg);
  pointer-events: none;
}

@keyframes shake {
  0%,
  92%,
  100% {
    transform: translate(0, 0) rotate(0deg);
  }
  94% {
    transform: translate(-3px, 1px) rotate(-1.5deg);
  }
  96% {
    transform: translate(3px, -1px) rotate(1.5deg);
  }
  98% {
    transform: translate(-2px, 0) rotate(-1deg);
  }
}

/* ── Copy ── */
.error-title {
  font-family: var(--font-bang);
  font-size: clamp(1.5rem, 5vw, 2.25rem);
  font-weight: 400;
  letter-spacing: 1px;
  line-height: 1.2;
  margin: 1rem 0 0.75rem;
  color: var(--text);
}

.error-desc {
  font-family: var(--font-body);
  font-size: 15px;
  line-height: 1.7;
  color: var(--text-muted);
  max-width: 420px;
  margin: 0 auto;
}

.comic-pre {
  background: var(--bg-surface);
  border: 3px solid var(--ink);
  border-radius: 4px;
  padding: 0.75rem 1rem;
  margin: 1.25rem 0 0;
  font-size: 13px;
  line-height: 1.6;
  text-align: left;
  overflow-x: auto;
  box-shadow: 4px 4px 0 var(--ink);
}

.comic-pre code {
  font-family: "SF Mono", "Fira Code", monospace;
  color: var(--text-muted);
  white-space: pre-wrap;
  word-break: break-word;
}

/* ── Actions ── */
.actions {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.75rem;
  margin-top: 1.75rem;
}

.btn {
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 0.95rem;
  letter-spacing: 0.5px;
  text-transform: uppercase;
  border: 3px solid var(--ink);
  border-radius: 3px;
  padding: 6px 22px;
  min-height: 42px;
  cursor: pointer;
  box-shadow: 4px 4px 0 var(--ink);
  transition: all 0.1s;
}

.btn:hover {
  transform: translate(-2px, -2px);
  box-shadow: 6px 6px 0 var(--ink);
}

.btn:active {
  transform: translate(1px, 1px);
  box-shadow: 1px 1px 0 var(--ink);
}

.btn-primary {
  background: var(--yellow);
  color: #1a1a2e;
}

.btn-primary:hover {
  background: var(--accent);
}

.btn-ghost {
  background: var(--bg-surface);
  color: var(--text);
}

.btn-ghost:hover {
  background: var(--accent2);
  color: #fff;
}

/* ── Help bubble ── */
.help-bubble {
  display: inline-block;
  background: var(--bg-card);
  border: 3px solid var(--ink);
  border-radius: 12px;
  padding: 0.6rem 1.5rem;
  box-shadow: 4px 4px 0 var(--ink);
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 0.9rem;
  color: var(--text-sub);
  position: relative;
}

.help-bubble::after {
  content: "";
  position: absolute;
  top: -16px;
  left: 50%;
  transform: translateX(-50%);
  border: 8px solid transparent;
  border-bottom-color: var(--ink);
}

.help-bubble::before {
  content: "";
  position: absolute;
  top: -11px;
  left: 50%;
  transform: translateX(-50%);
  border: 7px solid transparent;
  border-bottom-color: var(--bg-card);
  z-index: 1;
}

.help-link {
  color: var(--accent);
  text-decoration: underline;
  text-underline-offset: 3px;
}

.help-link:hover {
  color: var(--accent2);
}

/* ── Mobile ── */
@media (max-width: 640px) {
  .error-page {
    padding: 1.5rem 0;
    gap: 1.5rem;
  }

  .comic-panel {
    padding: 2rem 1.25rem 1.5rem;
    box-shadow: 5px 5px 0 var(--ink);
  }

  .error-code {
    text-shadow:
      3px 3px 0 var(--accent2),
      6px 6px 0 var(--ink);
  }

  /* tuck the burst into the corner: clear of the badge, off the digits */
  .oops-burst {
    top: -0.35rem;
    right: -1.25rem;
    font-size: 1.15rem;
  }

  .actions {
    flex-direction: column;
    align-items: stretch;
  }
}

@media (prefers-reduced-motion: reduce) {
  .error-code {
    animation: none;
  }
}
</style>
