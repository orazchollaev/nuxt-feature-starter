<script setup lang="ts">
import { nextTick, onBeforeUnmount, onMounted, ref, watch } from "vue"

const props = withDefaults(
  defineProps<{
    dotRadius?: number
    dotSpacing?: number
    cursorRadius?: number
    cursorForce?: number
    bulgeOnly?: boolean
    bulgeStrength?: number
    glowRadius?: number
    sparkle?: boolean
    waveAmplitude?: number
    gradientFrom?: string
    gradientTo?: string
    glowColor?: string
    className?: string
  }>(),
  {
    dotRadius: 1.5,
    dotSpacing: 19,
    cursorRadius: 500,
    cursorForce: 0.1,
    bulgeOnly: true,
    bulgeStrength: 67,
    glowRadius: 160,
    sparkle: false,
    waveAmplitude: 0,
    gradientFrom: "rgba(124, 255, 103, 0.35)",
    gradientTo: "rgba(160, 255, 188, 0.25)",
    glowColor: "#14110E",
    className: "",
  }
)

const TWO_PI = Math.PI * 2

type Dot = {
  ax: number
  ay: number
  sx: number
  sy: number
  vx: number
  vy: number
  x: number
  y: number
}

const root = ref<HTMLDivElement | null>(null)
const canvas = ref<HTMLCanvasElement | null>(null)
const glowEl = ref<SVGCircleElement | null>(null)

const glowId = `dot-field-glow-${Math.random().toString(36).slice(2, 9)}`

let dots: Dot[] = []

const mouse = {
  x: -9999,
  y: -9999,
  prevX: -9999,
  prevY: -9999,
  speed: 0,
}

let size = {
  w: 0,
  h: 0,
  offsetX: 0,
  offsetY: 0,
}

let glowOpacity = 0
let engagement = 0

let raf = 0
let resizeTimer: ReturnType<typeof setTimeout>
let speedInterval: ReturnType<typeof setInterval>
let frameCount = 0

// Idle pause: once the field has settled (no cursor influence, no drift left)
// we stop scheduling rAF entirely instead of redrawing a static field at 60fps.
let isRunning = false
let idleFrames = 0
const IDLE_FRAMES_TO_STOP = 90

// Gradient only depends on size + colors, not per-frame state — build it
// once per resize instead of allocating a new CanvasGradient every frame.
let cachedGradient: CanvasGradient | null = null

// Lifted to module-instance scope so cleanup() can remove the exact same
// function references — anonymous listeners in onBeforeUnmount would
// silently no-op and leak the rAF loop + window listeners on every unmount.
let onResize: (() => void) | null = null
let onMouseMove: ((e: MouseEvent) => void) | null = null

function buildDots(w: number, h: number) {
  const step = props.dotRadius + props.dotSpacing

  const cols = Math.floor(w / step)
  const rows = Math.floor(h / step)

  const padX = (w % step) / 2
  const padY = (h % step) / 2

  const nextDots: Dot[] = new Array(rows * cols)

  let idx = 0

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      const ax = padX + col * step + step / 2
      const ay = padY + row * step + step / 2

      nextDots[idx++] = {
        ax,
        ay,
        sx: ax,
        sy: ay,
        vx: 0,
        vy: 0,
        x: ax,
        y: ay,
      }
    }
  }

  dots = nextDots
}

function updateMouseSpeed() {
  const dx = mouse.prevX - mouse.x
  const dy = mouse.prevY - mouse.y

  const dist = Math.sqrt(dx * dx + dy * dy)

  mouse.speed += (dist - mouse.speed) * 0.5

  if (mouse.speed < 0.001) {
    mouse.speed = 0
  }

  mouse.prevX = mouse.x
  mouse.prevY = mouse.y
}

function setupCanvas() {
  if (!root.value || !canvas.value) return

  const ctx = canvas.value.getContext("2d", {
    alpha: true,
  })

  if (!ctx) return

  const dpr = Math.min(window.devicePixelRatio || 1, 1.5)

  function rebuildGradient() {
    if (!ctx || size.w === 0 || size.h === 0) return

    const grad = ctx.createLinearGradient(0, 0, size.w, size.h)
    grad.addColorStop(0, props.gradientFrom)
    grad.addColorStop(1, props.gradientTo)
    cachedGradient = grad
  }

  function doResize() {
    if (!root.value || !canvas.value || !ctx) return

    const rect = root.value.getBoundingClientRect()

    const w = rect.width
    const h = rect.height

    canvas.value.width = w * dpr
    canvas.value.height = h * dpr

    canvas.value.style.width = `${w}px`
    canvas.value.style.height = `${h}px`

    ctx.setTransform(dpr, 0, 0, dpr, 0, 0)

    // The field lives in a `position: fixed` layer, so it never moves with
    // scroll — track offsets with clientX/clientY (viewport-relative), not
    // pageX/pageY + scroll, or the cursor desyncs the further down you scroll.
    size = {
      w,
      h,
      offsetX: rect.left,
      offsetY: rect.top,
    }

    rebuildGradient()
    buildDots(w, h)
  }

  // Colors (e.g. light/dark theme toggle) are passed as reactive props but
  // the gradient is an imperative canvas object — without this watcher a
  // theme switch never repaints it, and if the field had idle-paused it
  // stays frozen on the old theme's colors indefinitely.
  watch(
    () => [props.gradientFrom, props.gradientTo],
    () => {
      rebuildGradient()

      idleFrames = 0

      if (!isRunning) {
        isRunning = true
        raf = requestAnimationFrame(tick)
      }
    }
  )

  onResize = () => {
    clearTimeout(resizeTimer)
    resizeTimer = setTimeout(doResize, 100)
  }

  onMouseMove = (e: MouseEvent) => {
    mouse.x = e.clientX - size.offsetX
    mouse.y = e.clientY - size.offsetY

    idleFrames = 0

    if (!isRunning) {
      isRunning = true
      raf = requestAnimationFrame(tick)
    }
  }

  function tick() {
    if (!ctx) return

    frameCount++

    const { w, h } = size

    const t = frameCount * 0.02

    const targetEngagement = Math.min(mouse.speed / 5, 1)

    engagement += (targetEngagement - engagement) * 0.06

    if (engagement < 0.001) {
      engagement = 0
    }

    glowOpacity += (engagement - glowOpacity) * 0.08

    if (glowEl.value) {
      glowEl.value.setAttribute("cx", String(mouse.x))
      glowEl.value.setAttribute("cy", String(mouse.y))
      glowEl.value.style.opacity = String(glowOpacity)
    }

    ctx.clearRect(0, 0, w, h)

    if (cachedGradient) {
      ctx.fillStyle = cachedGradient
    }

    const crSq = props.cursorRadius * props.cursorRadius

    const rad = props.dotRadius / 2

    ctx.beginPath()

    for (let i = 0; i < dots.length; i++) {
      const d = dots[i]!

      const dx = mouse.x - d.ax
      const dy = mouse.y - d.ay

      const distSq = dx * dx + dy * dy

      if (distSq < crSq && engagement > 0.01) {
        const dist = Math.sqrt(distSq)

        const angle = Math.atan2(dy, dx)

        if (props.bulgeOnly) {
          const falloff = 1 - dist / props.cursorRadius

          const push = falloff * falloff * props.bulgeStrength * engagement

          d.sx += (d.ax - Math.cos(angle) * push - d.sx) * 0.15
          d.sy += (d.ay - Math.sin(angle) * push - d.sy) * 0.15
        } else {
          const safeDist = Math.max(dist, 0.001)

          const move = (500 / safeDist) * (mouse.speed * props.cursorForce)

          d.vx += Math.cos(angle) * -move
          d.vy += Math.sin(angle) * -move
        }
      } else if (props.bulgeOnly) {
        d.sx += (d.ax - d.sx) * 0.1
        d.sy += (d.ay - d.sy) * 0.1
      }

      if (!props.bulgeOnly) {
        d.vx *= 0.9
        d.vy *= 0.9

        d.x = d.ax + d.vx
        d.y = d.ay + d.vy

        d.sx += (d.x - d.sx) * 0.1
        d.sy += (d.y - d.sy) * 0.1
      }

      let drawX = d.sx
      let drawY = d.sy

      if (props.waveAmplitude > 0) {
        drawY += Math.sin(d.ax * 0.03 + t) * props.waveAmplitude

        drawX += Math.cos(d.ay * 0.03 + t * 0.7) * props.waveAmplitude * 0.5
      }

      if (props.sparkle) {
        const hash = ((i * 2654435761) ^ (frameCount >> 3)) >>> 0

        if (hash % 100 < 3) {
          ctx.moveTo(drawX + rad * 1.8, drawY)
          ctx.arc(drawX, drawY, rad * 1.8, 0, TWO_PI)
        } else {
          ctx.moveTo(drawX + rad, drawY)
          ctx.arc(drawX, drawY, rad, 0, TWO_PI)
        }
      } else {
        ctx.moveTo(drawX + rad, drawY)
        ctx.arc(drawX, drawY, rad, 0, TWO_PI)
      }
    }

    ctx.fill()

    // Wave/sparkle variants animate continuously by design — only the plain
    // cursor-reactive field is eligible to go to sleep.
    const canIdle = props.waveAmplitude === 0 && !props.sparkle

    if (canIdle && engagement === 0 && mouse.speed === 0 && glowOpacity < 0.002) {
      idleFrames++
    } else {
      idleFrames = 0
    }

    if (idleFrames > IDLE_FRAMES_TO_STOP) {
      isRunning = false
      return
    }

    raf = requestAnimationFrame(tick)
  }

  doResize()

  window.addEventListener("resize", onResize)

  window.addEventListener("mousemove", onMouseMove, {
    passive: true,
  })

  speedInterval = setInterval(updateMouseSpeed, 20)

  raf = requestAnimationFrame(tick)
}

function cleanup() {
  cancelAnimationFrame(raf)

  clearInterval(speedInterval)

  clearTimeout(resizeTimer)

  if (onResize) window.removeEventListener("resize", onResize)
  if (onMouseMove) window.removeEventListener("mousemove", onMouseMove)
}

watch(
  () => [props.dotRadius, props.dotSpacing],
  async () => {
    await nextTick()

    if (size.w > 0 && size.h > 0) {
      buildDots(size.w, size.h)
    }
  }
)

onMounted(() => {
  setupCanvas()
})

onBeforeUnmount(() => {
  cleanup()
})
</script>

<template>
  <div ref="root" class="dot-field" :class="className">
    <canvas ref="canvas" class="dot-field-canvas" />

    <svg class="dot-field-glow-layer" aria-hidden="true">
      <defs>
        <radialGradient :id="glowId">
          <stop offset="0%" :stop-color="glowColor" />

          <stop offset="100%" stop-color="transparent" />
        </radialGradient>
      </defs>

      <circle
        ref="glowEl"
        cx="-9999"
        cy="-9999"
        :r="glowRadius"
        :fill="`url(#${glowId})`"
        style="opacity: 0; will-change: opacity"
      />
    </svg>
  </div>
</template>

<style scoped>
.dot-field {
  position: relative;
  width: 100%;
  height: 100%;
  overflow: hidden;
}

.dot-field-canvas,
.dot-field-glow-layer {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
}

.dot-field-glow-layer {
  pointer-events: none;
}
</style>
