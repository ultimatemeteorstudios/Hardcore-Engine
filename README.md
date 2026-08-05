# Hardcore Game Engine Series

A family of lightweight, zero-dependency, single-file HTML5 Canvas game engines built for JavaScript developers.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

---

## Overview & Architecture Evolution

The **Hardcore Engine** series evolved across three major generations, transforming from a simple Canvas 2D rendering loop into a production-ready, zero-GC, multi-backend WebGL 2D game engine.

```
+------------------+      +------------------+      +------------------+
|    Hardcore 1    | ---> |    Hardcore 2    | ---> |    Hardcore 3    |
|  Basic Canvas2D  |      |  Hybrid WebGL +  |      |   Zero-GC Pools  |
|  Single Loop     |      |  Asset/UI Engine |      | Scene Management |
+------------------+      +------------------+      +------------------+
```

---

## Detailed Version Comparison Matrix

| Architectural Feature | Hardcore 1.x | Hardcore 2.x (v3.0.0) | Hardcore 3.x (v4.3) |
| :--- | :--- | :--- | :--- |
| **Primary Renderer** | Canvas 2D | WebGL with 2D Fallback | Optimized Hybrid WebGL + 2D |
| **Memory Strategy** | Continuous `new` allocations | Manual Group management | **Zero-GC Object Pooling (`engine.pool`)** |
| **Scene Architecture** | Manual `if/else` states | Manual `if/else` states | **Modular Scene Lifecycle (`engine.scenes`)** |
| **Procedural Textures** | Not supported | Canvas 2D to WebGL texture | Canvas 2D dynamic GPU auto-upload |
| **UI System** | Manual HTML/DOM | Basic UI Builder (`createScreen`) | Automated Reactive HTML UI Engine |
| **Event Emitter** | Standard callbacks | Emitter with context bindings | Memory-leak-free scoped Emitter |
| **Collision Helpers** | basic AABB | Circle, AABB, Circle-Rect | Spatial Circle, AABB, Circle-Rect |

---

## Version Overviews

### 1. Hardcore 1 (Legacy)
- **Focus**: Minimalist HTML5 Canvas 2D game loop.
- **Key Characteristics**: Single main-loop structure, raw context access, lightweight utility functions.
- **Use Case**: Quick prototypes, small retro games, educational game dev.

### 2. Hardcore 2 Engine (v3.0.0)
- **Focus**: WebGL hardware acceleration & asset pipeline.
- **Key Features**:
  - Dual-backend renderer (`auto`, `webgl`, `2d`).
  - Unified rendering API (`renderer.drawImage`, `renderer.drawRect`, `renderer.drawCircle`).
  - Asset preloader & offscreen dynamic procedural textures (`engine.assets`).
  - Particle and Starfield effect modules.
  - UI Screen creation overlays (`engine.ui`).

### 3. Hardcore 3 Engine (v4.3) [Recommended]
- **Focus**: Performance optimization, zero Garbage Collection pauses, and structured game architecture.
- **Key Features**:
  - **Zero-Allocation Object Pool (`engine.pool`)**: Recycles entity instances, completely removing runtime GC spikes.
  - **Scene Management Engine (`engine.scenes`)**: Full `enter`, `update`, `draw`, and `exit` lifecycle hooks with automatic string-driven transitions.
  - **Stabilized Event Binding**: Fixed `this` context binding errors across event emitters and listeners.
  - **Enhanced UI & Audio Pipeline**: Fully code-driven DOM overlay generator and audio management.

---

## Migration Guide: Upgrading from v2 to v3

### 1. Updating the Game Loop to Scene Architecture

#### Hardcore 2 Syntax:
```javascript
engine.start({
  update(dt, timeScale) {
    // Monolithic update logic
  },
  draw(renderer, timeScale) {
    // Monolithic render logic
  }
});
```

#### Hardcore 3 Syntax:
```javascript
engine.scenes.add('gameplay', {
  enter(data) { /* Initialize scene */ },
  update(dt, timeScale) {
    // Return scene name to switch automatically
    if (gameOver) return 'gameOverScene';
  },
  draw(renderer, timeScale) { /* Render scene */ },
  exit() { /* Cleanup scene */ }
});

engine.scenes.set('gameplay');
engine.start();
```

---

### 2. Replacing Dynamic Instantiation with Object Pooling

#### Hardcore 2 (Garbage Collection Heavy):
```javascript
const bullets = [];
function fireBullet(x, y) {
  bullets.push({ x, y, speed: 10 });
}
```

#### Hardcore 3 (Zero-GC Optimization):
```javascript
const bulletPool = engine.pool.create({
  create: () => ({ x: 0, y: 0, speed: 10, dead: true }),
  size: 100
});

function fireBullet(x, y) {
  const bullet = bulletPool.get();
  if (bullet) {
    bullet.x = x;
    bullet.y = y;
    bullet.dead = false;
  }
}
```

---

## Master Quick Start (Hardcore 3 Engine)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hardcore Engine Multi-Version Template</title>
  <style>
    body { margin: 0; background: #000; display: flex; justify-content: center; align-items: center; height: 100vh; }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>

  <script src="hardcore3.js"></script>
  <script>
    const engine = Hardcore3.init({
      canvas: 'gameCanvas',
      renderer: 'auto',
      fitMode: 'square',
      width: 800,
      height: 800,
      background: '#050510'
    });

    engine.scenes.add('main', {
      update(dt) {},
      draw(renderer) {
        renderer.clear('#050510');
        renderer.drawCircle(400, 400, 50, '#00ffcc');
      }
    });

    engine.scenes.set('main');
    engine.start();
  </script>
</body>
</html>
```

---

## License

All engine versions are open-source and distributed under the terms of the MIT License.

```text
Copyright © 2026 <copyright holders>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE. 
```
