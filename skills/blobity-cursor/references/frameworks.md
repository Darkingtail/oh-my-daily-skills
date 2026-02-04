# Framework Templates

Complete Blobity integration templates for React, Vue 3, and Vue 2.

All templates include: touch device detection, light/dark theme switching, scroll bounce, and cleanup on unmount.

---

## React (Hook)

### useBlobity.ts

```tsx
import { useEffect, useRef } from 'react';
import Blobity from 'blobity';

interface BlobityTheme {
  color: string;
  dotColor: string;
  fontColor: string;
}

const darkTheme: BlobityTheme = {
  color: '#ffffff',
  dotColor: '#10b981',
  fontColor: '#0d1117',
};

const lightTheme: BlobityTheme = {
  color: '#190a11',
  dotColor: '#111827',
  fontColor: '#000000',
};

/**
 * Detect current theme. Adapt the selector to your project:
 * - data-theme attribute: document.documentElement.getAttribute('data-theme')
 * - class-based (Tailwind): document.documentElement.classList.contains('dark')
 * - media query: window.matchMedia('(prefers-color-scheme: dark)').matches
 */
function isDark(): boolean {
  return document.documentElement.getAttribute('data-theme') !== 'light';
}

function getTheme(): BlobityTheme {
  return isDark() ? darkTheme : lightTheme;
}

export function useBlobity(options?: {
  /** CSS selector for elements the cursor should wrap */
  focusableElements?: string;
  /** Enable scroll bounce effect */
  scrollBounce?: boolean;
  /** Only activate on pages matching this selector */
  pageSelector?: string;
}) {
  const blobityRef = useRef<Blobity | null>(null);

  useEffect(() => {
    // Skip touch devices
    const isTouchDevice =
      'ontouchstart' in window || navigator.maxTouchPoints > 0;
    if (isTouchDevice) return;

    // Optional: only activate on specific pages
    if (
      options?.pageSelector &&
      !document.querySelector(options.pageSelector)
    ) {
      return;
    }

    // Prevent double init
    if (document.body.classList.contains('blobity-active')) return;

    const theme = getTheme();

    const blobity = new Blobity({
      licenseKey: 'opensource',
      invert: true,
      zIndex: 50,
      color: theme.color,
      dotColor: theme.dotColor,
      radius: 6,
      magnetic: false,
      mode: 'normal',
      focusableElements:
        options?.focusableElements ??
        'a, button, [data-blobity], [data-blobity-tooltip]',
      focusableElementsOffsetX: 5,
      focusableElementsOffsetY: 4,
      font: "'JetBrains Mono', monospace",
      fontSize: 16,
      fontWeight: 600,
      fontColor: theme.fontColor,
      tooltipPadding: 12,
    } as any);

    blobityRef.current = blobity;
    document.body.classList.add('blobity-active');

    // Theme observer
    const observer = new MutationObserver(() => {
      const t = getTheme();
      blobity.updateOptions({
        color: t.color,
        dotColor: t.dotColor,
        fontColor: t.fontColor,
      });
    });

    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['data-theme', 'class'],
    });

    // Scroll bounce
    let scrollTimeout: ReturnType<typeof setTimeout> | null = null;
    const onScroll = () => {
      if (scrollTimeout) return;
      scrollTimeout = setTimeout(() => {
        blobity.bounce();
        scrollTimeout = null;
      }, 150);
    };

    if (options?.scrollBounce !== false) {
      window.addEventListener('scroll', onScroll, { passive: true });
    }

    // Cleanup
    return () => {
      observer.disconnect();
      window.removeEventListener('scroll', onScroll);
      document.body.classList.remove('blobity-active');
      blobity.destroy();
      blobityRef.current = null;
    };
  }, []);

  return blobityRef;
}
```

### Usage in Component

```tsx
import { useBlobity } from './useBlobity';

export default function LandingPage() {
  useBlobity({
    focusableElements: 'a, button, .card, [data-blobity-tooltip]',
    scrollBounce: true,
    pageSelector: '.hero-section', // Only activate when hero exists
  });

  return (
    <main className='hero-section'>
      <h1>Welcome</h1>
      <div className='card' data-blobity-tooltip='Learn more'>
        Feature Card
      </div>
      <button>Get Started</button>
    </main>
  );
}
```

### Global CSS (add to your stylesheet)

```css
body.blobity-active,
body.blobity-active a,
body.blobity-active button,
body.blobity-active [data-blobity],
body.blobity-active [data-blobity-tooltip] {
  cursor: none !important;
}
```

---

## Vue 3 (Composable)

### useBlobity.ts

```ts
import { onMounted, onUnmounted, ref } from 'vue';
import type { Ref } from 'vue';
import Blobity from 'blobity';

interface BlobityTheme {
  color: string;
  dotColor: string;
  fontColor: string;
}

const darkTheme: BlobityTheme = {
  color: '#ffffff',
  dotColor: '#10b981',
  fontColor: '#0d1117',
};

const lightTheme: BlobityTheme = {
  color: '#190a11',
  dotColor: '#111827',
  fontColor: '#000000',
};

function isDark(): boolean {
  return document.documentElement.getAttribute('data-theme') !== 'light';
}

function getTheme(): BlobityTheme {
  return isDark() ? darkTheme : lightTheme;
}

export function useBlobity(options?: {
  focusableElements?: string;
  scrollBounce?: boolean;
  pageSelector?: string;
}): Ref<Blobity | null> {
  const blobityRef = ref<Blobity | null>(null);
  let observer: MutationObserver | null = null;
  let scrollTimeout: ReturnType<typeof setTimeout> | null = null;

  const onScroll = () => {
    if (scrollTimeout) return;
    scrollTimeout = setTimeout(() => {
      blobityRef.value?.bounce();
      scrollTimeout = null;
    }, 150);
  };

  onMounted(() => {
    const isTouchDevice =
      'ontouchstart' in window || navigator.maxTouchPoints > 0;
    if (isTouchDevice) return;

    if (
      options?.pageSelector &&
      !document.querySelector(options.pageSelector)
    ) {
      return;
    }

    if (document.body.classList.contains('blobity-active')) return;

    const theme = getTheme();

    const blobity = new Blobity({
      licenseKey: 'opensource',
      invert: true,
      zIndex: 50,
      color: theme.color,
      dotColor: theme.dotColor,
      radius: 6,
      magnetic: false,
      mode: 'normal',
      focusableElements:
        options?.focusableElements ??
        'a, button, [data-blobity], [data-blobity-tooltip]',
      focusableElementsOffsetX: 5,
      focusableElementsOffsetY: 4,
      font: "'JetBrains Mono', monospace",
      fontSize: 16,
      fontWeight: 600,
      fontColor: theme.fontColor,
      tooltipPadding: 12,
    } as any);

    blobityRef.value = blobity;
    document.body.classList.add('blobity-active');

    // Theme observer
    observer = new MutationObserver(() => {
      const t = getTheme();
      blobity.updateOptions({
        color: t.color,
        dotColor: t.dotColor,
        fontColor: t.fontColor,
      });
    });

    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['data-theme', 'class'],
    });

    // Scroll bounce
    if (options?.scrollBounce !== false) {
      window.addEventListener('scroll', onScroll, { passive: true });
    }
  });

  onUnmounted(() => {
    observer?.disconnect();
    window.removeEventListener('scroll', onScroll);
    document.body.classList.remove('blobity-active');
    blobityRef.value?.destroy();
    blobityRef.value = null;
  });

  return blobityRef;
}
```

### Usage in Component

```vue
<script setup lang="ts">
import { useBlobity } from './useBlobity';

const blobity = useBlobity({
  focusableElements: 'a, button, .card, [data-blobity-tooltip]',
  scrollBounce: true,
});
</script>

<template>
  <main class="hero-section">
    <h1>Welcome</h1>
    <div class="card" data-blobity-tooltip="Learn more">Feature Card</div>
    <button>Get Started</button>
  </main>
</template>

<style>
body.blobity-active,
body.blobity-active a,
body.blobity-active button,
body.blobity-active [data-blobity],
body.blobity-active [data-blobity-tooltip] {
  cursor: none !important;
}
</style>
```

---

## Vue 2 (Mixin)

### blobityMixin.js

```js
import Blobity from 'blobity';

const darkTheme = {
  color: '#ffffff',
  dotColor: '#10b981',
  fontColor: '#0d1117',
};

const lightTheme = {
  color: '#190a11',
  dotColor: '#111827',
  fontColor: '#000000',
};

function isDark() {
  return document.documentElement.getAttribute('data-theme') !== 'light';
}

function getTheme() {
  return isDark() ? darkTheme : lightTheme;
}

export default {
  data() {
    return {
      _blobity: null,
      _blobityObserver: null,
      _blobityScrollTimeout: null,
    };
  },

  mounted() {
    this.$nextTick(() => {
      this._initBlobity();
    });
  },

  beforeDestroy() {
    this._destroyBlobity();
  },

  methods: {
    _initBlobity() {
      const isTouchDevice =
        'ontouchstart' in window || navigator.maxTouchPoints > 0;
      if (isTouchDevice) return;

      // Override these in your component if needed
      const pageSelector = this.blobityPageSelector || null;
      if (pageSelector && !document.querySelector(pageSelector)) return;

      if (document.body.classList.contains('blobity-active')) return;

      const theme = getTheme();

      this._blobity = new Blobity({
        licenseKey: 'opensource',
        invert: true,
        zIndex: 50,
        color: theme.color,
        dotColor: theme.dotColor,
        radius: 6,
        magnetic: false,
        mode: 'normal',
        focusableElements:
          this.blobityFocusableElements ||
          'a, button, [data-blobity], [data-blobity-tooltip]',
        focusableElementsOffsetX: 5,
        focusableElementsOffsetY: 4,
        font: "'JetBrains Mono', monospace",
        fontSize: 16,
        fontWeight: 600,
        fontColor: theme.fontColor,
        tooltipPadding: 12,
      });

      document.body.classList.add('blobity-active');

      // Theme observer
      this._blobityObserver = new MutationObserver(() => {
        const t = getTheme();
        this._blobity.updateOptions({
          color: t.color,
          dotColor: t.dotColor,
          fontColor: t.fontColor,
        });
      });

      this._blobityObserver.observe(document.documentElement, {
        attributes: true,
        attributeFilter: ['data-theme', 'class'],
      });

      // Scroll bounce
      this._onBlobityScroll = () => {
        if (this._blobityScrollTimeout) return;
        this._blobityScrollTimeout = setTimeout(() => {
          this._blobity && this._blobity.bounce();
          this._blobityScrollTimeout = null;
        }, 150);
      };
      window.addEventListener('scroll', this._onBlobityScroll, {
        passive: true,
      });
    },

    _destroyBlobity() {
      if (this._blobityObserver) {
        this._blobityObserver.disconnect();
        this._blobityObserver = null;
      }
      if (this._onBlobityScroll) {
        window.removeEventListener('scroll', this._onBlobityScroll);
      }
      document.body.classList.remove('blobity-active');
      if (this._blobity) {
        this._blobity.destroy();
        this._blobity = null;
      }
    },
  },
};
```

### Usage in Component

```vue
<template>
  <main class="hero-section">
    <h1>Welcome</h1>
    <div class="card" data-blobity-tooltip="Learn more">Feature Card</div>
    <button>Get Started</button>
  </main>
</template>

<script>
import blobityMixin from './blobityMixin';

export default {
  mixins: [blobityMixin],

  // Optional: customize via data properties
  data() {
    return {
      blobityFocusableElements: 'a, button, .card, [data-blobity-tooltip]',
      blobityPageSelector: '.hero-section',
    };
  },
};
</script>

<style>
body.blobity-active,
body.blobity-active a,
body.blobity-active button,
body.blobity-active [data-blobity],
body.blobity-active [data-blobity-tooltip] {
  cursor: none !important;
}
</style>
```

---

## Astro

For Astro (SSG/SSR), create a `.astro` component:

### BlobityCursor.astro

```astro
---
/**
 * BlobityCursor - Drop into any Astro page
 * Supports light/dark theme, Astro view transitions
 */
---

<style is:global>
  body.blobity-active,
  body.blobity-active a,
  body.blobity-active button,
  body.blobity-active [data-blobity],
  body.blobity-active [data-blobity-tooltip] {
    cursor: none !important;
  }
</style>

<script>
  import Blobity from 'blobity';

  function initBlobity() {
    const isTouchDevice =
      'ontouchstart' in window || navigator.maxTouchPoints > 0;
    if (isTouchDevice) return;
    if (document.body.classList.contains('blobity-active')) return;

    const isDark = () =>
      document.documentElement.getAttribute('data-theme') !== 'light';

    const blobity = new Blobity({
      licenseKey: 'opensource',
      invert: true,
      zIndex: 50,
      dotColor: isDark() ? '#10b981' : '#111827',
      color: isDark() ? '#ffffff' : '#190a11',
      radius: 6,
      magnetic: false,
      mode: 'normal',
      focusableElements:
        'a, button, [data-blobity], [data-blobity-tooltip]',
      focusableElementsOffsetX: 5,
      focusableElementsOffsetY: 4,
      font: "'JetBrains Mono', monospace",
      fontSize: 16,
      fontWeight: 600,
      fontColor: isDark() ? '#0d1117' : '#000000',
      tooltipPadding: 12,
    } as any);

    document.body.classList.add('blobity-active');

    const observer = new MutationObserver(() => {
      blobity.updateOptions({
        dotColor: isDark() ? '#10b981' : '#111827',
        color: isDark() ? '#ffffff' : '#190a11',
        fontColor: isDark() ? '#0d1117' : '#000000',
      });
    });

    observer.observe(document.documentElement, {
      attributes: true,
      attributeFilter: ['data-theme'],
    });

    let scrollTimeout: ReturnType<typeof setTimeout> | null = null;
    const onScroll = () => {
      if (scrollTimeout) return;
      scrollTimeout = setTimeout(() => {
        blobity.bounce();
        scrollTimeout = null;
      }, 150);
    };
    window.addEventListener('scroll', onScroll, { passive: true });

    // Cleanup on Astro page navigation
    document.addEventListener(
      'astro:before-swap',
      () => {
        observer.disconnect();
        window.removeEventListener('scroll', onScroll);
        document.body.classList.remove('blobity-active');
        blobity.destroy();
      },
      { once: true },
    );
  }

  if (document.readyState === 'loading') {
    document.addEventListener('DOMContentLoaded', initBlobity);
  } else {
    initBlobity();
  }

  document.addEventListener('astro:page-load', initBlobity);
</script>
```

Usage: `<BlobityCursor />` in any `.astro` or `.mdx` page.
