# Mobile & Touch Interaction

Mobile devices require special handling. Touch events, screen size, and performance differ from desktop.

---

## Touch-Enabled OrbitControls

```javascript
import { OrbitControls } from "https://unpkg.com/three@0.170.0/examples/jsm/controls/OrbitControls.js";

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;

// Touch-specific settings
controls.enablePan = true;
controls.enableZoom = true;
controls.enableRotate = true;

// Touch gestures mapping
controls.touches = {
  ONE: THREE.TOUCH.ROTATE, // Single finger: rotate
  TWO: THREE.TOUCH.DOLLY_PAN, // Two fingers: zoom + pan
};

// Limit zoom range for mobile
controls.minDistance = 2;
controls.maxDistance = 20;

// Limit vertical rotation (prevent flip)
controls.minPolarAngle = 0.1;
controls.maxPolarAngle = Math.PI - 0.1;
```

---

## Custom Touch Handlers

```javascript
// Track touch state
let touchStartX = 0;
let touchStartY = 0;
let isTouching = false;

renderer.domElement.addEventListener(
  "touchstart",
  (e) => {
    e.preventDefault();
    isTouching = true;
    touchStartX = e.touches[0].clientX;
    touchStartY = e.touches[0].clientY;
  },
  { passive: false },
);

renderer.domElement.addEventListener(
  "touchmove",
  (e) => {
    if (!isTouching) return;
    e.preventDefault();

    const touchX = e.touches[0].clientX;
    const touchY = e.touches[0].clientY;

    const deltaX = (touchX - touchStartX) / window.innerWidth;
    const deltaY = (touchY - touchStartY) / window.innerHeight;

    // Rotate object based on touch movement
    mesh.rotation.y += deltaX * 2;
    mesh.rotation.x += deltaY * 2;

    touchStartX = touchX;
    touchStartY = touchY;
  },
  { passive: false },
);

renderer.domElement.addEventListener("touchend", () => {
  isTouching = false;
});
```

---

## Pinch-to-Zoom

```javascript
let initialPinchDistance = 0;
let initialScale = 1;

renderer.domElement.addEventListener("touchstart", (e) => {
  if (e.touches.length === 2) {
    initialPinchDistance = Math.hypot(
      e.touches[0].clientX - e.touches[1].clientX,
      e.touches[0].clientY - e.touches[1].clientY,
    );
    initialScale = mesh.scale.x;
  }
});

renderer.domElement.addEventListener(
  "touchmove",
  (e) => {
    if (e.touches.length === 2) {
      e.preventDefault();
      const currentDistance = Math.hypot(
        e.touches[0].clientX - e.touches[1].clientX,
        e.touches[0].clientY - e.touches[1].clientY,
      );

      const scale = initialScale * (currentDistance / initialPinchDistance);
      const clampedScale = Math.max(0.5, Math.min(3, scale));
      mesh.scale.setScalar(clampedScale);
    }
  },
  { passive: false },
);
```

---

## Mobile Performance Optimization

```javascript
// Detect mobile
const isMobile = /Android|iPhone|iPad|iPod/i.test(navigator.userAgent);

// Adjust settings for mobile
if (isMobile) {
  // Lower pixel ratio (better performance)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 1.5));

  // Reduce shadow map size
  mainLight.shadow.mapSize.width = 1024;
  mainLight.shadow.mapSize.height = 1024;

  // Disable expensive effects
  renderer.shadowMap.enabled = false;

  // Use simpler materials
  const material = new THREE.MeshPhongMaterial({ color: 0x44aa88 });
  // Instead of MeshStandardMaterial (PBR is expensive)
}
```

---

## Responsive Canvas

```javascript
function onWindowResize() {
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
  renderer.setSize(window.innerWidth, window.innerHeight);
}

window.addEventListener("resize", onWindowResize);
window.addEventListener("orientationchange", () => {
  // Delay to let browser update dimensions
  setTimeout(onWindowResize, 100);
});
```

---

## Prevent Default Touch Behaviors

```css
/* Prevent pull-to-refresh, zoom, etc. */
html,
body {
  touch-action: none;
  overflow: hidden;
  overscroll-behavior: none;
}

canvas {
  touch-action: none;
}
```

---

## Unified Pointer Events (Touch + Mouse)

```javascript
// Modern approach: use pointer events for both touch and mouse
renderer.domElement.addEventListener("pointerdown", (e) => {
  // Works for mouse click AND touch start
  console.log("Pointer down:", e.pointerId, e.pointerType);
});

renderer.domElement.addEventListener("pointermove", (e) => {
  // Works for mouse move AND touch move
  const x = (e.clientX / window.innerWidth) * 2 - 1;
  const y = -(e.clientY / window.innerHeight) * 2 + 1;
  // Use x, y for raycasting or interaction
});

renderer.domElement.addEventListener("pointerup", (e) => {
  // Works for mouse up AND touch end
});
```
