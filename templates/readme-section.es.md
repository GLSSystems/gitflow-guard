## Flujo de trabajo

Este repositorio usa [gitflow-guard](https://github.com/GLSSystems/gitflow-guard), versión `vX.Y.Z` (fijada en `.github/workflows/ci.yml`):

- `main` es producción y solo cambia por un PR fusionado por una cuenta de `MAIN_APPROVERS`.
- Ramas, roles, ciclo diario, hotfix y vuelta atrás: [Workflow](https://github.com/GLSSystems/gitflow-guard/blob/main/docs/en/workflow.md) (en inglés).
- Colaboradores externos y cuentas de automatización trabajan desde un fork: [Automation accounts](https://github.com/GLSSystems/gitflow-guard/blob/main/docs/en/automation-accounts.md) (en inglés).

Después de clonar:

```sh
git config core.hooksPath .githooks
```

Propio de este proyecto:

| Tema | Detalle |
|---|---|
| Validación | <qué revisa el job `validate`> |
| Publicación | <dónde y cómo publica el job `deploy`> |
| Variables y secretos | `MAIN_APPROVERS` + <los del proyecto> |
| Verificación tras publicar | <URL o comprobaciones> |
