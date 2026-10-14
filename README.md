# Carrossel 3D

> Slides posicionados em um círculo 3D, girando com controles de mouse e inércia.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **CSS 3D transforms** (`perspective` + `preserve-3d`) — sem Three.js
- **requestAnimationFrame** para inércia e snap
- **CSS puro** — sem pré-processador

## Requisitos

- Node.js 18+

## Como executar

```bash
npm install
npm run dev
```

Abra http://localhost:5173 no navegador.

## Scripts

| Script | Descrição |
| --- | --- |
| `npm run dev` | Servidor de desenvolvimento (Vite) |
| `npm run build` | Build de produção |
| `npm run preview` | Serve o build de produção |

## Estrutura

```
src/
  main.js              entry: cria o app e importa o CSS
  App.vue              cena 3D, giro, inércia, snap, slide frontal
  styles.css           stage, carousel, slides, floor, sombra
```

### Funcionalidades

- 8 slides em círculo 3D via `rotateY(i * 45deg) translateZ(raio)`
- Raio calculado: `(larguraDoSlide / 2) / tan(π / n)`
- Giro com drag do mouse (pointer events, `touch-action: none`)
- Inércia ao soltar (`ω *= 0.95` por frame)
- Snap suave para o múltiplo de 45° mais próximo (transition 400ms ease-out)
- Slide frontal em destaque (opacity 1, brightness 1.15)
- Slides de trás com opacidade reduzida (0.6, brightness 0.7)
- Sombra elíptica no "chão" abaixo do carrossel

> **Adaptação:** a descrição original sugere Three.js, mas o stack definido é Vue + CSS puro sem libs — o 3D sai de `perspective` + `preserve-3d` (CSS 3D transforms), mantendo 100% do comportamento descrito.


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/carousel-3d-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.
