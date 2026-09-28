# Carrossel 3D

> Slides posicionados em um círculo 3D, girando com controles de mouse e inércia.

> **Adaptação:** a descrição original sugere Three.js, mas o stack definido é Vue + CSS puro sem libs — o 3D sai de `perspective` + `preserve-3d` (CSS 3D transforms), mantendo 100% do comportamento descrito.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css`
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/styles.css`
- App.vue único — ângulo (`θ`) e velocidade angular (`ω`) como refs reativos

## Implementação

### 1. Cena CSS 3D

- `.stage { perspective: 1200px }` → `.carousel { transform-style: preserve-3d; transform: rotateY(θ) }`
- 8 slides, cada um com `transform: rotateY(i * 45deg) translateZ(raio)` e `backface-visibility: hidden`
- `raio = (larguraDoSlide / 2) / tan(π / n)`
- Slides com gradiente/tema + número; sombra elíptica no "chão" abaixo do carrossel para assentar a cena

### 2. Giro com o mouse

- `pointerdown` no stage → `setPointerCapture`; `touch-action: none`
- `pointermove`: `θ += dx * 0.3`, aplicado direto (sem transition durante o arrasto)
- Guardar as últimas posições do ponteiro para calcular a velocidade angular `ω` no momento de soltar

### 3. Inércia + snap

- Loop `requestAnimationFrame`: `θ += ω; ω *= 0.95`
- Quando `|ω| < 0.05`: snap suave para o múltiplo de 45° mais próximo (transition 400ms ease-out)
- Slide frontal em destaque (escala/brilho maiores), slides de trás com opacidade reduzida — calculado a partir de θ

## Checklist — 100% da descrição

- [ ] Slides posicionados em círculo 3D
- [ ] Giro com drag do mouse
- [ ] Inércia ao soltar + snap no slide mais próximo
- [ ] Slide frontal em destaque
