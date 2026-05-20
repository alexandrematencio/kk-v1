<script setup lang="ts">
const baseURL = useRuntimeConfig().app.baseURL

const route = useRoute()
const router = useRouter()

const isVisible = ref(false)
const isMenuOpen = ref(false)

let threshold = 0

function updateThreshold() {
  if (typeof window === 'undefined') return
  // Home: appears once we have entirely passed the hero (100vh).
  // Inner pages: appears as soon as we scroll past the in-flow nav.
  threshold = route.path === '/' ? window.innerHeight - 60 : 120
}

function onScroll() {
  if (typeof window === 'undefined') return
  isVisible.value = window.scrollY > threshold
}

function onResize() {
  updateThreshold()
  onScroll()
}

async function handleMenuClick(e: MouseEvent) {
  e.preventDefault()
  isMenuOpen.value = false
  if (route.path === '/') {
    document.querySelector('.menu-section')?.scrollIntoView({ behavior: 'smooth' })
    return
  }
  await router.push('/')
  requestAnimationFrame(() => {
    requestAnimationFrame(() => {
      document.querySelector('.menu-section')?.scrollIntoView({ behavior: 'smooth' })
    })
  })
}

function closeMenu() {
  isMenuOpen.value = false
}

function onKeydown(e: KeyboardEvent) {
  if (e.key === 'Escape') closeMenu()
}

onMounted(() => {
  updateThreshold()
  onScroll()
  window.addEventListener('scroll', onScroll, { passive: true })
  window.addEventListener('resize', onResize, { passive: true })
  window.addEventListener('keydown', onKeydown)
})

onBeforeUnmount(() => {
  if (typeof window === 'undefined') return
  window.removeEventListener('scroll', onScroll)
  window.removeEventListener('resize', onResize)
  window.removeEventListener('keydown', onKeydown)
})

watch(() => route.path, () => {
  isMenuOpen.value = false
  nextTick(() => {
    updateThreshold()
    onScroll()
  })
})

watch(isMenuOpen, (open) => {
  if (typeof document === 'undefined') return
  document.body.style.overflow = open ? 'hidden' : ''
})
</script>

<template>
  <Transition name="sticky-fade">
    <div v-if="isVisible" class="sticky-nav">
      <nav class="sticky-nav-inner">
        <NuxtLink to="/" class="sticky-brand" aria-label="Accueil">
          <img :src="`${baseURL}logos/logo-khan-kluay-v2.svg`" alt="Khan Kluay" class="sticky-brand-logo" />
        </NuxtLink>

        <div class="sticky-items">
          <NuxtLink to="/#menu" class="sticky-item" @click="handleMenuClick">La carte</NuxtLink>
          <NuxtLink to="/reserver" class="sticky-item">Réserver</NuxtLink>
          <NuxtLink to="/commander" class="sticky-item">Commander</NuxtLink>
          <NuxtLink to="/contact" class="sticky-item">Contact</NuxtLink>
        </div>

        <button
          class="burger-btn"
          :class="{ open: isMenuOpen }"
          :aria-expanded="isMenuOpen"
          aria-label="Menu"
          @click="isMenuOpen = !isMenuOpen"
        >
          <span></span>
          <span></span>
          <span></span>
        </button>
      </nav>
    </div>
  </Transition>

  <Transition name="overlay">
    <div v-if="isMenuOpen" class="burger-overlay" @click.self="closeMenu">
      <nav class="burger-menu">
        <NuxtLink to="/" class="burger-item" @click="closeMenu">Accueil</NuxtLink>
        <NuxtLink to="/#menu" class="burger-item" @click="handleMenuClick">La carte</NuxtLink>
        <NuxtLink to="/reserver" class="burger-item" @click="closeMenu">Réserver</NuxtLink>
        <NuxtLink to="/commander" class="burger-item" @click="closeMenu">Commander</NuxtLink>
        <NuxtLink to="/contact" class="burger-item" @click="closeMenu">Contact</NuxtLink>
      </nav>
    </div>
  </Transition>
</template>

<style scoped>
.sticky-nav {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  background: rgba(247, 247, 247, 0.94);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border-bottom: 1px solid var(--color-border);
}

.sticky-nav-inner {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 32px;
  gap: 1.5rem;
  max-width: 1400px;
  margin: 0 auto;
}

.sticky-brand {
  display: flex;
  align-items: center;
  text-decoration: none;
  flex-shrink: 0;
  transition: opacity 0.2s ease;
}

.sticky-brand:hover {
  opacity: 0.7;
}

.sticky-brand-logo {
  height: 40px;
  width: auto;
  display: block;
}

.sticky-items {
  display: flex;
  gap: 2.5rem;
  align-items: center;
}

.sticky-item {
  font-family: 'Inter', sans-serif;
  font-size: 17px;
  font-weight: 500;
  color: #000;
  text-decoration: none;
  letter-spacing: 0.02em;
  transition: color 0.2s ease;
}

.sticky-item:hover {
  color: #001B95;
}

/* Burger button — hidden on desktop */
.burger-btn {
  display: none;
}

/* Sticky-fade transition */
.sticky-fade-enter-active,
.sticky-fade-leave-active {
  transition: opacity 0.3s ease, transform 0.3s ease;
}

.sticky-fade-enter-from,
.sticky-fade-leave-to {
  opacity: 0;
  transform: translateY(-12px);
}

/* MOBILE: hide brand+items, show burger top-right */
@media (max-width: 768px) {
  .sticky-nav-inner {
    padding: 10px 16px;
    justify-content: flex-end;
  }

  .sticky-brand,
  .sticky-items {
    display: none;
  }

  .burger-btn {
    display: flex;
    flex-direction: column;
    justify-content: center;
    gap: 5px;
    width: 44px;
    height: 44px;
    background: #fff;
    border: 1px solid #000;
    border-radius: 50%;
    cursor: pointer;
    padding: 12px;
  }

  .burger-btn span {
    display: block;
    width: 100%;
    height: 2px;
    background: #001B95;
    border-radius: 1px;
    transform-origin: center;
    transition: transform 0.25s ease, opacity 0.25s ease;
  }

  .burger-btn.open span:nth-child(1) {
    transform: translateY(7px) rotate(45deg);
  }

  .burger-btn.open span:nth-child(2) {
    opacity: 0;
  }

  .burger-btn.open span:nth-child(3) {
    transform: translateY(-7px) rotate(-45deg);
  }
}

/* Mobile overlay menu */
.burger-overlay {
  position: fixed;
  inset: 0;
  background: rgba(247, 247, 247, 0.97);
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  z-index: 200;
  display: flex;
  align-items: center;
  justify-content: center;
}

.burger-menu {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.75rem;
  padding: 2rem;
}

.burger-item {
  font-family: 'Inter', sans-serif;
  font-size: 32px;
  font-weight: 600;
  color: #000;
  text-decoration: none;
  letter-spacing: 0.02em;
  transition: color 0.2s ease;
}

.burger-item:hover,
.burger-item.router-link-active {
  color: #001B95;
}

.overlay-enter-active,
.overlay-leave-active {
  transition: opacity 0.25s ease;
}

.overlay-enter-from,
.overlay-leave-to {
  opacity: 0;
}
</style>
