<script setup lang="ts">
import { gsap } from 'gsap'

const route = useRoute()
const router = useRouter()
const isHome = computed(() => route.path === '/')

async function handleMenuClick(e: MouseEvent) {
  e.preventDefault()
  if (isHome.value) {
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

onMounted(() => {
  if (!isHome.value) return
  gsap.fromTo('.nav-item',
    { x: -400 },
    {
      x: 0,
      duration: 0.9,
      stagger: 0.18,
      ease: 'back.out(1.7)',
    },
  )
})
</script>

<template>
  <nav class="nav-bar" :class="{ 'is-home': isHome }">
    <div class="nav-group nav-group-left">
      <NuxtLink v-if="!isHome" to="/" class="nav-item nav-back" aria-label="Retour a l'accueil">
        ←
      </NuxtLink>
      <NuxtLink to="/#menu" class="nav-item" @click="handleMenuClick">
        La carte
        <span class="nav-thai" aria-hidden="true">เมนู</span>
      </NuxtLink>
      <NuxtLink to="/reserver" class="nav-item">
        Réserver
        <span class="nav-thai" aria-hidden="true">จอง</span>
      </NuxtLink>
    </div>
    <div class="nav-group nav-group-right">
      <NuxtLink to="/commander" class="nav-item">
        Commander
        <span class="nav-thai" aria-hidden="true">สั่งอาหาร</span>
      </NuxtLink>
      <NuxtLink to="/contact" class="nav-item">
        Contact
        <span class="nav-thai" aria-hidden="true">ติดต่อ</span>
      </NuxtLink>
    </div>
  </nav>
</template>

<style scoped>
/* DESKTOP: nav-group wrappers dissolve so items become direct flex children
   of .nav-bar with space-between (matches template.pen). */
.nav-bar {
  position: relative;
  z-index: 10;
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 0;
  gap: 10px;
}

.nav-group {
  display: contents;
}

.nav-item {
  position: relative;
  font-family: 'Inter', sans-serif;
  font-size: 40px;
  line-height: 1;
  font-weight: 500;
  color: #000000;
  cursor: pointer;
  transition: opacity 0.2s ease;
  text-decoration: none;
}

/* Thai translation that drops on hover (desktop only) */
.nav-thai {
  position: absolute;
  top: 100%;
  left: 50%;
  margin-top: 8px;
  font-family: 'Inter', sans-serif;
  font-size: 0.625em;
  font-weight: 400;
  color: #E22B02;
  letter-spacing: 0;
  white-space: nowrap;
  pointer-events: none;
  opacity: 0;
  transform: translate(-50%, -2.5em);
  transition: opacity 0.25s ease, transform 0.25s ease;
}

@media (hover: hover) and (min-width: 769px) {
  .nav-item:hover .nav-thai {
    animation: thai-drop-bounce 0.75s cubic-bezier(0.5, 0, 0.55, 1) forwards;
  }
}

@keyframes thai-drop-bounce {
  0% {
    opacity: 0;
    transform: translate(-50%, -2.5em);
  }
  25% {
    opacity: 1;
  }
  /* Hits the surface */
  42% {
    transform: translate(-50%, 0);
  }
  /* First rebound */
  55% {
    transform: translate(-50%, -0.55em);
  }
  /* Second hit */
  68% {
    transform: translate(-50%, 0);
  }
  /* Smaller rebound */
  78% {
    transform: translate(-50%, -0.22em);
  }
  /* Third hit */
  88% {
    transform: translate(-50%, 0);
  }
  /* Micro rebound */
  94% {
    transform: translate(-50%, -0.06em);
  }
  100% {
    opacity: 1;
    transform: translate(-50%, 0);
  }
}

/* Pre-animation initial state — only on home, GSAP animates to x:0 */
.nav-bar.is-home .nav-item {
  transform: translateX(-400px);
}

@media (max-width: 1024px) {
  .nav-item {
    font-size: 32px;
  }
}

/* MOBILE: revert to 2-column stacked layout. */
@media (max-width: 768px) {
  .nav-thai {
    display: none;
  }

  .nav-bar {
    align-items: flex-start;
    gap: 1rem;
  }

  .nav-group {
    display: flex;
    flex-direction: column;
    gap: 0.2em;
  }

  .nav-group-left {
    align-items: flex-start;
    text-align: left;
  }

  .nav-group-right {
    align-items: flex-end;
    text-align: right;
  }

  .nav-item {
    font-size: 28px;
  }

  /* Inner pages: shift right column down by one row so it aligns with "La carte". */
  .nav-bar:not(.is-home) .nav-group-right {
    padding-top: calc(28px + 0.2em);
  }
}

@media (max-width: 480px) {
  .nav-item {
    font-size: 21px;
  }

  .nav-bar:not(.is-home) .nav-group-right {
    padding-top: calc(21px + 0.2em);
  }
}
</style>
