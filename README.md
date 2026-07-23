# jogadev-skills

Marketplace personal de skills de [Claude Code](https://code.claude.com/docs/en/plugins), reutilizables e instalables en cualquier equipo.

> **Regla del repo**: solo contenido **genérico y sin datos sensibles**. Nada de tokens, passwords, keys, IPs privadas, hosts/dominios reales de infraestructura propia ni rutas de acceso. Lo específico de una máquina vive en esa máquina, no aquí.

## Instalar

```text
/plugin marketplace add JoseGarcia2001/jogadev-skills
/plugin install jogadev@jogadev-skills
```

Actualizar más tarde:

```text
/plugin marketplace update jogadev-skills
/plugin update jogadev@jogadev-skills
```

## Contenido

Plugin **`jogadev`**:

| Skill | Para qué |
|---|---|
| `pendientes` | Bolsa única de pendientes personales: captura, lista, filtra y completa tareas en un índice grepable (`~/pendientes/`, datos fuera del repo). |
| `self-hosting-runbook` | Plantilla para montar/operar servicios self-hosted tras un túnel saliente (sin abrir puertos). |

Las skills son *model-invoked*: Claude las activa solo cuando su `description` calza con la tarea.

## Estructura

```text
.claude-plugin/marketplace.json     # manifiesto del marketplace
plugins/jogadev/
├─ .claude-plugin/plugin.json        # manifiesto del plugin
└─ skills/<nombre>/SKILL.md          # una skill por carpeta
```

## Añadir una skill

1. `plugins/jogadev/skills/<nombre>/SKILL.md` con frontmatter `name` + `description` (la `description` es el gatillo: "Úsalo cuando…").
2. Cuerpo conciso; empuja el detalle a archivos hermanos si crece (progressive disclosure).
3. Commit + push. Quien lo tenga instalado actualiza con `/plugin update`.

## Licencia

MIT.
