---
name: pendientes
description: Bolsa única y local de pendientes personales. Úsala cuando el usuario diga "anota X", "pendiente:", "apunta X", "mis pendientes", "qué tengo pendiente (de un área)", "marca X como hecha" o "/pendientes". Captura tareas de una línea o con cuerpo extenso, lista, filtra por tag y completa — siempre on-demand, nunca por iniciativa propia.
---

# Pendientes — bolsa única, local y grepable

Una sola bolsa para todos los pendientes, de cualquier área o proyecto. Los datos son personales y viven solo en la máquina del usuario — esta skill es únicamente el mecanismo.

**Bolsa**: `~/pendientes/` (en cada máquina, ese path puede ser un symlink a donde el usuario guarde sus datos). Si no existe, créala: `INDEX.md` con solo `# Pendientes` + carpeta `tareas/`.

- `INDEX.md` — una línea por tarea, SIEMPRE. Única fuente de verdad del estado.
- `tareas/` — un archivo por tarea, solo cuando la tarea tiene cuerpo (párrafo o más).

## Formato

```
- [ ] Título corto #area (YYYY-MM-DD)
- [ ] Título corto (para: YYYY-MM-DD) #area (YYYY-MM-DD) → [[YYYY-MM-DD-slug]]
- [x] Tarea hecha #area (YYYY-MM-DD)
```

- **El orden del índice ES la prioridad**: la línea de arriba es lo más importante. Sin campos de prioridad. Las `[x]` van al fondo. Capturas nuevas: append al final de las abiertas, salvo que el usuario indique urgencia.
- Tags de área en kebab-case, uno o más por tarea. Si el usuario no da área, infiérela de la conversación; en última instancia `#inbox`.
- Fecha = día de captura, zona horaria del usuario.
- `(para: YYYY-MM-DD)` = deadline, SOLO cuando existe uno real (sesión, compromiso, entrega). No inventar fechas: un deadline falso se vence en silencio y devalúa los demás.
- Archivo de cuerpo: `tareas/YYYY-MM-DD-slug.md`. Texto libre, fiel a lo que soltó el usuario (limpiar muletillas, no resumir). Sin frontmatter y sin estado — el estado vive solo en `INDEX.md`.

## Operaciones

**Capturar** ("anota X"): append de una línea a `INDEX.md`. Sin preguntas de vuelta salvo ambigüedad real — capturar en segundos y devolver al usuario a lo que estaba haciendo. Si suelta un párrafo o una idea extensa: crear `tareas/YYYY-MM-DD-slug.md` con el cuerpo + línea en el índice terminada en ` → [[YYYY-MM-DD-slug]]`.

**Listar / filtrar** ("mis pendientes", "qué tengo de tal área"): grep sobre `INDEX.md` y mostrar solo las líneas que aplican. NO leer archivos de `tareas/` para listar.

```bash
grep '\[ \]' ~/pendientes/INDEX.md                    # abiertas
grep '\[ \]' ~/pendientes/INDEX.md | grep '#red-habi' # abiertas de un área
grep -ril 'palabra' ~/pendientes/tareas/              # buscar en cuerpos
```

**Detalle** ("dame el detalle de X"): Read del archivo linkeado — solo entonces.

**Completar** ("marca X como hecha"): editar `[ ]` → `[x]` en `INDEX.md` y mover la línea al fondo. El archivo de cuerpo se queda como histórico. Nunca borrar tareas salvo pedido explícito.

**Priorizar / repriorizar** ("repriorícemos", "recap de tareas"): leer el índice completo, proponer un orden con lógica explícita (deadlines primero, prerequisitos antes que dependientes, research al final), dejar que el usuario ajuste, y reordenar las líneas. Al pasar, preguntar por deadlines reales de las tareas que huelan a tenerlos — solo esas.

**Archivar** (solo si el usuario lo pide): mover las líneas `[x]` a `ARCHIVO.md` junto al índice.

## Reglas

- **On-demand estricto**: nunca listar, resumir ni recordar pendientes sin petición explícita del usuario.
- **Estado solo en `INDEX.md`**: nunca duplicar estado en los archivos de cuerpo.
- **Nada sale de la máquina**: el contenido de la bolsa jamás va a repos compartidos, commits de proyectos ni servicios remotos.
