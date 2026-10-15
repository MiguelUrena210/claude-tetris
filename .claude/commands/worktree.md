---
description: Crea un git worktree en .trees/[nombre] y ejecuta ahí las instrucciones de forma aislada
argument-hint: <instrucciones a ejecutar en el worktree>
---

Instrucciones del usuario: $ARGUMENTS

## Pasos

1. Si `$ARGUMENTS` está vacío, pide las instrucciones y detente.
2. Deriva `[nombre]` de las instrucciones: kebab-case, minúsculas, sin acentos, máx. 4 palabras, descriptivo (ej. `agregar-hold-piece`). Si `.trees/[nombre]` o la rama ya existen, añade sufijo `-2`, `-3`…
3. Crea el worktree desde la rama actual, con rama nueva del mismo nombre:
   ```bash
   git worktree add .trees/[nombre] -b [nombre]
   ```
4. Asegura que `.trees/` esté en `.gitignore` (añádelo si falta).
5. Lanza un agente (`Agent`, `subagent_type: general-purpose`, `run_in_background: true`) con un prompt autocontenido que incluya:
   - Las instrucciones del usuario, completas.
   - Ruta absoluta del worktree: `<repo>/.trees/[nombre]`.
   - Regla estricta: trabajar SOLO dentro de esa ruta. Usar rutas absolutas bajo el worktree en Read/Edit/Write y `cd` a él en Bash. Nunca tocar el árbol principal ni otros worktrees.
   - Leer `CLAUDE.md` del worktree y respetar sus convenciones.
   - Al terminar: commit en la rama `[nombre]` (sin push, sin merge a `main`) y reportar archivos cambiados y cómo verificar.
6. Responde al usuario en una línea: nombre, ruta y rama. Al terminar el agente, relata su resultado y recuerda cómo integrar (`git merge [nombre]`) y limpiar (`git worktree remove .trees/[nombre]`).

## Límites

- No hagas merge, push ni borres el worktree sin que el usuario lo pida.
- No edites archivos del árbol principal.
