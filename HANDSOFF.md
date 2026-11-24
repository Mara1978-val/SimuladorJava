# HANDSOFF.md — JavaLearn DAM

Handoff para la próxima sesión. Actualizar al terminar cada tarea grande.

## Estado actual (última actualización: 2026-11-24)

- App estática con **600 retos** (3 × 200: Bloques / Parsons / Micro-Editor; 50 por nivel: Fácil, Medio, Avanzado y ⭐ Reto 10) y motor Java WASM real.
- **Landing de bienvenida**: panel `panel-home` + pestaña «Inicio»; se muestra solo la 1ª vez (flag `javalearn_dam_seen` en localStorage) y siempre vuelve desde la pestaña.
- **Tema claro/oscuro**: interruptor 🌙/☀️ en cabecera; variables CSS en `css/theme.css` (`:root` oscuro, `[data-theme="light"]` claro); persistencia en `javalearn_dam_theme`; respeta `prefers-color-scheme` en la 1ª visita; `theme-color` sincronizado.
- **PWA completa**: `manifest.webmanifest` (standalone, 3 iconos) + `icons/` (SVG fuente → PNG con inkscape) + `sw.js` con 3 capas de caché: shell (precache + stale-while-revalidate), CDN (Tailwind/Fonts, respuestas opacas) y motor unpkg (cache-first). Offline verificado con recarga en modo red caído. **Botón «📱 Instalar»** en la cabecera (`initInstall` en `app.js`, `beforeinstallprompt`; oculto en iOS Safari, que no emite el evento).
- **Idioma por defecto: valenciano (VA)** (`currentLang = 'va'` en `js/i18n.js`, `setLanguage('va')` en `initApp`, `lang="va"` en `index.html`); el botón ES de la cabecera cambia a castellano en caliente.
- **Botón «➡ Siguiente ejercicio»** en los 3 paneles, junto al desplegable: avanza dentro del filtro activo (y cicla al primero al llegar al final), sin abrir el select.
- **Auditoría 500/500 PASS, 0 fallos** (verificada el 2026-11-24 con Chrome headless sobre `python -m http.server`): 200 compilaciones de Bloques + 150 de Parsons (dedup) + 150 soluciones del Micro-Editor = 500 ejecuciones reales cubriendo los 600 retos. Lighthouse: 100/100/100/100 (a11y/best-practices/SEO/agentic).
- **MCPs globales**: `packet-tracer`, `chrome-devtools` (ya cargado en OpenCode), `codegraph` (índice `.codegraph/` refrescado con `codegraph init` tras cambios grandes).
- **Docs**: `AGENTS.md` (convenciones) y este `HANDSOFF.md`, ambos actualizados a 600 retos / 500 compilaciones.

## Qué se hizo en la última sesión

1. **Nivel extra ⭐ Reto 10** (commit `e6f3d36`): +50 retos por modo (600 en total). Catálogos `topics.js` (`TOPICS_MASTER`) y `editor-methods.js` (`EDITOR_METHODS_MASTER`), generadores `activities.js` (3 × 200), filtros `master` en los 3 paneles, i18n ES/VA (102 claves, paridad verificada), barra de progreso `x/600`, PWA y textos de la landing.
2. **Auditoría extendida a los 600 retos** (`tools/audit.html`): nueva sección de Parsons (compilación de las soluciones correctas), tarjeta y registro `audit.parsons`; cabecera y logs actualizados; total = 200 + 150 + 150 = 500 compilaciones únicas; `window.__audit` expone `blocks`, `parsons`, `editor`, `failures`.
3. **Fix de `mcdRecursivo`** (`data/editor-methods.js`): la solución oficial usaba una llamada recursiva al método con un nombre que el driver no genera; se sustituyó por Euclides iterativo y se ajustaron las descripciones ES/VA. Los 15 métodos Reto 10 del Micro-Editor pasan sus tests con el motor real.
4. **Documentación sincronizada**: `README.md`, `AGENTS.md`, `HANDSOFF.md` y comentarios de `topics.js` ya no mencionan 450/300 ni 3 × 150.
5. **Verificación e2e**: auditoría completa en Chrome headless (500/500, `failures: []`, 0 errores de consola), `node --check` en todos los `.js` y `javac` sobre las 150 soluciones Reto 10 (0 errores).
6. **Botón «Siguiente ejercicio»** (sesión del 24/11): helper `nextActivityId()` en `js/utils.js`, botón `btn-next-{block,parsons,editor}` en `index.html`, listeners en los 3 módulos y claves i18n `next_exercise`/`next_exercise_aria` (104/104).
7. **Valenciano por defecto** (sesión del 24/11): `currentLang = 'va'`, `setLanguage('va')` al iniciar y `lang="va"`; el botón ES sigue disponible para cambiar en caliente.

## Pendientes / ideas futuras

- [ ] Refrescar índice codegraph tras cambios grandes: `codegraph init`.
- [ ] Actualizar caché del SW al publicar (los bumps de `javalearn-shell-v1`/`cdn-v1` purgan versiones viejas automáticamente).
- [ ] Idea: precarga en idle del wasm (7,7 MB) tras la landing para que la 1ª compilación no espere descarga.

## Cómo verificar rápidamente

```bash
python3 -m http.server 8080
# → http://localhost:8080/
```

1. Borrar localStorage (`javalearn_dam_prog`, `javalearn_dam_seen`, `javalearn_dam_theme`) → 1ª visita muestra la landing → «Empezar» → Bloques.
2. Pestaña «Inicio» siempre accesible; ES/VA traduce la landing completa; los 4 filtros (incluido ⭐ Reto 10) funcionan en los 3 modos.
3. Toggle ☀️/🌙 persiste al recargar; sin flag, usa `prefers-color-scheme`.
4. Motor: botón «Compilar y Ejecutar» en Bloques descarga el wasm la 1ª vez y ejecuta de verdad.
5. Regresión fuerte: abrir `tools/audit.html` y esperar a `window.__audit.done === true` → **500/500, `failures: []`** (~15–20 min).
6. PWA: Application → Service Workers (activated) + cachés `javalearn-shell-v1`/`cdn-v1`; con la red caída, recargar sigue pintando la app.

## Claves de localStorage

| Clave | Contenido |
|---|---|
| `javalearn_dam_prog` | Progreso 3×200 (bloques/parsons/editor) |
| `javalearn_dam_seen` | «1» si el usuario ya vio la landing |
| `javalearn_dam_theme` | `light` \| `dark` |
