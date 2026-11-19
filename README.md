# llmops-lab

Proyecto base del laboratorio de LLMOps: un clasificador de tickets con evals, gate y monitoreo.

La estructura ya está creada y los archivos están vacíos. La guía indica qué va en cada uno:
[1. llmops-evals-gate-monitoreo.md](https://github.com/utec-devops-2026/laboratorios/blob/main/laboratorio_llmops/1.%20llmops-evals-gate-monitoreo.md)

## Cómo empezar

No trabajes sobre este repositorio: haz **fork** a tu cuenta (botón **Fork**) y clona tu fork.

```bash
git clone https://github.com/<tu-usuario>/llmops-lab.git
cd llmops-lab
```

## Qué archivo se llena en qué paso

| Paso | Archivo | Estado |
|---|---|---|
| 0 | `requirements.txt`, `.gitignore`, `src/__init__.py` | Listos |
| 1.1 | `src/llm.py` | Vacío |
| 1.2 | `config.json`, `src/tracing.py`, `src/app.py` | Vacíos |
| 1.3 | `data/golden.jsonl` | Vacío |
| 1.4 | `tests/test_app.py` | Vacío |
| 2.1 | `prompts/clasificador/v1.md` | Vacío |
| 2.2 | `src/evaluate.py` | Vacío |
| 2.3 | `gate/thresholds.json`, `gate/baseline.json`, `src/gate.py` | Vacíos |
| 2.4 | `prompts/clasificador/v2.md` | Vacío |
| 3.1 | `.github/workflows/llmops.yml` | Lo creas tú |
| 3.2 | `.github/CODEOWNERS` | Vacío |
| 4.1 | `src/traffic.py`, `src/replay.py` | Vacíos |
| 4.2 | `src/monitor.py` | Vacío |
| 4.5 | `.github/workflows/monitoring.yml` | Lo creas tú |

Los dos workflows no vienen creados a propósito: un archivo de workflow vacío hace que GitHub Actions marque un error en cada push.

`data/prod/` se llena al ejecutar `python -m src.traffic` (paso 4.1).

## Preguntas del laboratorio

**¿Por qué el gate rechazó el prompt v1 y qué cambiaste en v2 para que aprobara?**  
v1 no pedía categorías cerradas ni JSON y tampoco protegía contra instrucciones de tickets; por eso cayó a 0.2 de contrato válido y a 0 de inyecciones y accuracy. v2 define las cinco categorías y los tres niveles de churn, exige un objeto JSON sin texto adicional y añade la regla para tratar el ticket como datos.

**¿Por qué el pull request trampa falló en GitHub si en tu máquina el gate aprobaba?**  
En un PR el gate restaura desde `main` `gate/`, `data/golden.jsonl` y `src/evaluate.py`/`src/gate.py`, así que no confía en una versión local manipulada de las reglas o del juez. La aprobación local podía usar umbrales, casos o métricas alterados; GitHub volvió a ejecutar el juez autorizado y falló.

**El monitor respondió REVISAR en la semana price_hike. ¿Eso significa que el prompt está fallando? ¿Qué dato falta para saberlo?**  
No: REVISAR solo indica que la mezcla actual deriva mucho de la referencia (PSI 1.677 en price_hike) y el contrato sigue válido. Falta comparar las respuestas con etiquetas esperadas o una muestra de golden set etiquetada de esa semana para medir accuracy real y descartar una falsa alarma.

**¿Qué te muestra Langfuse que no ves en el archivo de trazas local?**  
Langfuse centraliza los request en una UI con tags de prompt y escenario, scores agregados, búsqueda e historial visual de latencia, tokens y errores. El JSONL local conserva una instantánea por request, pero no ofrece el dashboard ni el análisis agregado temporal de toda la operación.
