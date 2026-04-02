# linkers-android-ci-workflows

Workflows de GitHub Actions reutilizables y centralizados para todos los proyectos Android de la organización.

## Workflows disponibles

| Workflow | Propósito |
|---|---|
| `reusable-label-check.yml` | Valida que la PR tenga exactamente un label de release |
| `reusable-android-build.yml` | Compila un módulo Gradle y opcionalmente ejecuta lint |
| `reusable-android-release.yml` | Calcula el siguiente semver, crea el tag y el GitHub Release |

## Cómo consumirlos

En el archivo `.github/workflows/pr-checks.yml` de tu proyecto:

```yaml
jobs:
  label-check:
    uses: ChrisCorrea94/linkers-android-ci-workflows/.github/workflows/reusable-label-check.yml@v1

  build-check:
    needs: label-check
    uses: ChrisCorrea94/linkers-android-ci-workflows/.github/workflows/reusable-android-build.yml@v1
    with:
      module: network
```

En `.github/workflows/release.yml`:

```yaml
jobs:
  label-check:
    uses: ChrisCorrea94/linkers-android-ci-workflows/.github/workflows/reusable-label-check.yml@v1

  release:
    needs: label-check
    uses: ChrisCorrea94/linkers-android-ci-workflows/.github/workflows/reusable-android-release.yml@v1
    with:
      bump: ${{ needs.label-check.outputs.bump }}
    secrets:
      GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Labels requeridos

| Label | Bump | Cuándo usarlo |
|---|---|---|
| `💥 Major` | `x.0.0` | Cambio que rompe compatibilidad |
| `✨ Minor` | `0.x.0` | Funcionalidad nueva compatible |
| `🐛 Patch` | `0.0.x` | Bug fix, ajuste interno, docs |

## Versionado

Este repo se versiona a sí mismo usando los mismos workflows.
Los consumers deben pinear a un tag (`@v1`) para recibir cambios de forma deliberada.
