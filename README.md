# Grand Line: Duelo de Leyendas (versión reparada)

Juego fan no oficial de combates por turnos 3 vs 3 con 30 personajes.

## Cómo jugarlo
1. Descomprime el ZIP **manteniendo la carpeta `assets/` junto a `index.html`**.
2. Abre `index.html` con doble clic (Chrome, Edge, Firefox o Safari). No necesita servidor ni internet.
3. `index_standalone.html` es una copia con todas las imágenes incrustadas: funciona sola (pesa ~6,6 MB).

En iPhone/iPad: sube la carpeta a un hosting estático, o abre `index_standalone.html` desde Archivos.

## Estructura
- `index.html` – juego (HTML + CSS + JS en un solo archivo).
- `assets/sprites/characters/` – 30 sprites PNG con transparencia real.
- `assets/sprites/effects/` – 31 efectos PNG con transparencia (30 personajes; Kuzan tiene dos).
- `assets/backgrounds/stage.jpg` – escenario con los círculos azules eliminados.
- `manifest_sprites.json`, `manifest_effects.json`, `manifest_background.json` – relación personaje ↔ archivo.
- `INFORME.md` – errores corregidos, cambios de equilibrio, pruebas y dudas.

## Ajustes rápidos (en el script de `index.html`)
- `DAMAGE_SCALE` (1.0 = equilibrio original): súbelo (p. ej. 1.4) para combates más cortos.
- `AREA_FACTOR` (0.7): daño por objetivo de los ataques de área.
- Añade `?fast` a la URL para desactivar animaciones (útil para pruebas).
