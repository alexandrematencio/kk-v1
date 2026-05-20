<template>
  <div class="layout">
    <div class="layout-nav">
      <AppNav />
    </div>
    <slot />
  </div>
</template>

<style scoped>
.layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.layout-nav {
  padding: 0 32px;
  background: var(--color-bg);
}
</style>
