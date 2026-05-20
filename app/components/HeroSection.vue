<script setup lang="ts">
import { gsap } from 'gsap'

const ctaBtn = ref<HTMLButtonElement>()
const ctaSize = ref({ w: 0, h: 0 })
let ctaRO: ResizeObserver | null = null

function scrollToPhilosophy() {
  document.querySelector('#philosophie')?.scrollIntoView({ behavior: 'smooth' })
}

onMounted(() => {
  const tl = gsap.timeline()

  // Logo: starts at time 0, in sync with AppNav's own entry animation
  tl.fromTo('.hero-display-title',
    { y: 300 },
    {
      y: 0,
      duration: 0.9,
      ease: 'back.out(1.7)',
    },
    0,
  )

  // Discover button: starts after nav/logo finish
  tl.fromTo('.scroll-cta',
    { scale: 0, opacity: 0 },
    {
      scale: 1,
      opacity: 1,
      duration: 0.5,
      ease: 'back.out(4)',
    },
    '+=0.05',
  )

  if (ctaBtn.value) {
    const update = () => {
      if (!ctaBtn.value) return
      ctaSize.value = {
        w: ctaBtn.value.offsetWidth,
        h: ctaBtn.value.offsetHeight,
      }
    }
    update()
    ctaRO = new ResizeObserver(update)
    ctaRO.observe(ctaBtn.value)
  }
})

onUnmounted(() => {
  ctaRO?.disconnect()
})
</script>

<template>
  <section class="hero">
    <div class="hero-bg"></div>

    <AppNav />

    <div class="hero-content">
      <div class="right-bloc">
        <picture>
          <source media="(max-width: 768px)" srcset="/images/hero/responsive-mobile-img.png" />
          <img
            src="/images/hero/hero-banner-img.png"
            alt="Khan Kluay Thai Cuisine"
            class="hero-img"
          />
        </picture>
        <p class="cuisine-text">Cuisine thaïlandaise<img src="/flag-design.svg" alt="" class="cuisine-flag" />authentique</p>
        <button ref="ctaBtn" class="scroll-cta" @click="scrollToPhilosophy">
          <svg
            v-if="ctaSize.w > 0 && ctaSize.h > 0"
            class="cta-border"
            :viewBox="`-1 -1 ${ctaSize.w + 2} ${ctaSize.h + 2}`"
            preserveAspectRatio="none"
            aria-hidden="true"
          >
            <rect
              class="cta-border-path"
              x="0.5"
              y="0.5"
              :width="ctaSize.w - 1"
              :height="ctaSize.h - 1"
              :rx="Math.max(0, (ctaSize.h - 1) / 2)"
              :ry="Math.max(0, (ctaSize.h - 1) / 2)"
              pathLength="100"
            />
          </svg>
          <span class="cta-text">Discover ↓</span>
        </button>
      </div>
    </div>

    <div class="hero-display-title">
      <img
        src="/logos/logo-khan-kluay-v2.svg"
        alt="Khan Kluay"
        class="khan-kluay-logo"
      />
    </div>
  </section>
</template>

<style scoped>
.hero {
  position: relative;
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  padding: 0 32px;
  overflow: hidden;
  background: #faf4ef;
  box-sizing: border-box;
}

.hero-bg {
  position: absolute;
  inset: 0;
  background-image: url('/images/hero/hero-banner-img.png');
  background-size: cover;
  background-position: center;
  z-index: 1;
  opacity: 0;
}

.hero-content {
  position: relative;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  flex: 1;
}

.right-bloc {
  position: absolute;
  top: 60%;
  left: 50%;
  transform: translate(-50%, -50%);
  z-index: 50;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
}

.right-bloc picture {
  display: block;
  width: 100%;
  text-align: center;
}

.cuisine-text {
  margin-top: 0;
  font-family: 'Inter', sans-serif;
  font-size: 24px;
  font-weight: 500;
  color: #001B95;
}

.cuisine-flag {
  display: inline-block;
  height: calc(1em / 1.2);
  width: auto;
  vertical-align: -0.1em;
  margin: 0 0.25em;
}

.hero-img {
  max-width: 85%;
  height: auto;
  max-height: 305px;
  width: auto;
}

.scroll-cta {
  position: relative;
  margin-top: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 8px 40px;
  background: #ffffff;
  border: 1px solid #000000;
  border-radius: 60px;
  cursor: pointer;
  transition: border-color 0.25s ease;
}

.scroll-cta:hover {
  border-color: transparent;
}

.cta-border {
  position: absolute;
  inset: -1px;
  pointer-events: none;
  overflow: visible;
}

.cta-border-path {
  fill: none;
  stroke: #A51931;
  stroke-width: 1.5;
  stroke-linecap: round;
  stroke-linejoin: round;
  stroke-dasharray: 100;
  stroke-dashoffset: 100;
  transition: stroke-dashoffset 0.607s cubic-bezier(0.65, 0, 0.35, 1);
}

.scroll-cta:hover .cta-border-path {
  stroke-dashoffset: 0;
}

.cta-text {
  position: relative;
  z-index: 1;
  font-family: 'Inter', sans-serif;
  font-size: 48px;
  line-height: 1;
  font-weight: 500;
  color: #001B95;
}

.hero-display-title {
  position: relative;
  z-index: 10;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  padding: 0 32px;
  height: 360px;
  box-sizing: border-box;
}

.khan-kluay-logo {
  height: 360px;
  width: 100%;
  max-width: none;
  object-fit: fill;
  display: block;
}

@media (max-width: 1024px) {
  .right-bloc {
    width: 100%;
    max-width: 600px;
  }

  .cta-text {
    font-size: 32px;
  }
}

@media (max-width: 768px) {
  .hero {
    height: auto;
    min-height: 100vh;
    padding: 1rem 1.25rem;
    gap: 1.5rem;
  }

  .right-bloc {
    position: static;
    transform: none;
    width: 100%;
    max-width: 480px;
    margin: 0 auto;
    padding: 1.5rem 0;
  }

  .hero-img {
    max-height: 864px;
    max-width: 80%;
  }

  .cuisine-text {
    font-size: 1rem;
    text-align: center;
    line-height: 1.3;
  }

  .scroll-cta {
    padding: 8px 28px;
  }

  .cta-text {
    font-size: 22px;
    letter-spacing: 0.04em;
  }

  .hero-display-title {
    height: auto;
    padding: 0;
  }

  .khan-kluay-logo {
    height: auto;
    max-height: 160px;
    width: 100%;
    object-fit: contain;
  }
}

@media (max-width: 480px) {
  .hero-img {
    max-height: 672px;
    max-width: 80%;
  }

  .khan-kluay-logo {
    max-height: 120px;
  }

  .cta-text {
    font-size: 18px;
  }
}
</style>
