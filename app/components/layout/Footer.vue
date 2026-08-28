<template>
  <footer class="footer">
    <div class="container">
      <div class="speech-bubble">
        <p>
          {{ $t("layout.footer.builtWith") }}
          <span class="heart">♥</span>
          OrazChollaev
        </p>
      </div>
    </div>
  </footer>
</template>

<style scoped>
.footer {
  background: var(--bg);
  border-top: 4px solid var(--ink);
  margin-top: 3rem;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 1.5rem 2rem;
  text-align: center;
}

.speech-bubble {
  display: inline-block;
  background: var(--bg-card);
  border: 3px solid var(--ink);
  border-radius: 12px;
  padding: 0.6rem 1.5rem;
  box-shadow: 4px 4px 0 var(--ink);
  position: relative;
  font-family: var(--font-body);
  font-weight: 700;
  font-size: 0.95rem;
  color: var(--text-sub);
}

.speech-bubble::after {
  content: "";
  position: absolute;
  bottom: -16px;
  left: 50%;
  transform: translateX(-50%);
  border: 8px solid transparent;
  border-top-color: var(--ink);
}

.speech-bubble::before {
  content: "";
  position: absolute;
  bottom: -11px;
  left: 50%;
  transform: translateX(-50%);
  border: 7px solid transparent;
  border-top-color: var(--bg-card);
  z-index: 1;
}

.heart {
  color: #ef4444;
  animation: heartbeat 1.5s ease-in-out infinite;
  display: inline-block;
}

@keyframes heartbeat {
  0%,
  100% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.3);
  }
}
</style>
