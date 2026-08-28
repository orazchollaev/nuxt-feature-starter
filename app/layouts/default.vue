<!-- app/layouts/default.vue -->
<script setup lang="ts">
const colorMode = useColorMode()
</script>

<template>
  <div class="default-layout">
    <div class="default-layout-bg">
      <EffectsDotField
        :gradient-from="
          colorMode.value === 'dark' ? 'rgba(255, 183, 3, 0.35)' : 'rgba(5, 150, 105, 0.45)'
        "
        :gradient-to="
          colorMode.value === 'dark' ? 'rgba(255, 107, 107, 0.25)' : 'rgba(233, 69, 96, 0.35)'
        "
        :glow-color="colorMode.value === 'dark' ? '#ffb703' : '#059669'"
      />
    </div>

    <LayoutHeader />

    <slot />

    <LayoutFooter />
  </div>
</template>

<style>
.default-layout {
  position: relative;
  width: 100%;
}

.default-layout-bg {
  position: fixed;
  inset: 0;
  z-index: -1;
  pointer-events: none;
}
</style>
