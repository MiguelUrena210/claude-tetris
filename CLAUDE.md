# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Tetris en JavaScript vanilla + Canvas 2D. Sin dependencias, sin build, sin tests ni linter. Idioma de UI y docs: español.

## Ejecutar

```bash
open index.html                 # directo
python3 -m http.server 8000     # recomendado → http://localhost:8000
```

No hay suite de tests: verificar cambios jugando en el navegador.

## Arquitectura

- `game.js`: toda la lógica, script clásico (no módulo) con `'use strict'` y estado global mutable (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, `animId`…). `init()` resetea todo y también es el handler de reinicio.
- Índice de tipo de pieza = valor de celda = índice en `COLORS` (1–7; `0` vacío). `PIECES[i]` guarda matrices ya rellenas con `i`, así que `merge()` copia el valor directamente al `board`.
- Rotación: `rotateCW` (transpuesta + reverso) + wall kicks en `tryRotate` (`[0,-1,1,-2,2]` columnas). No es SRS.
- Ciclo: `loop` (rAF) acumula `dt` → al superar `dropInterval` baja pieza o `lockPiece()` (`merge` → `clearLines` → `spawn`). `spawn` con colisión → `endGame()`.
- Puntuación/nivel en `clearLines`: `LINE_SCORES[n] * level`; nivel = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)`. Hard drop +2/celda, soft drop +1/fila.
- Overlay único (`#overlay`) reutilizado para PAUSA y GAME OVER.

## Acoplamientos a mantener

- Canvas `#board` en `index.html` debe medir `COLS*BLOCK × ROWS*BLOCK` (300×600).
- `drawNext` asume preview 4×4 de 30px (`NB = 30`) ↔ `#next-canvas` 120×120.
- Controles listados en tres sitios: handler `keydown` de `game.js`, panel de `index.html` y tabla del README.

## Quirks conocidos (código actual)

- `togglePause` al reanudar no vuelve a ocultar el overlay.
- `endGame` llamado desde `loop` cancela el frame, pero `loop` vuelve a pedir `requestAnimationFrame` después → el bucle sigue tras Game Over.
