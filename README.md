# SimulAdapvector

Simulador de **cronogramas de proyectos mineros/constructivos** en Python. Genera cronogramas sintéticos pero realistas —con dependencias entre tareas, estados de avance coherentes, retrasos, buffers y costos— y los exporta a Excel junto con visualizaciones interactivas (Gantt, diagrama de red y dashboard comparativo) hechas con Plotly.

Está pensado como generador de datos de prueba y motor de simulación para herramientas de planificación y seguimiento de proyectos.

## Qué hace

Todo el código vive en `simul.py`, alrededor de la clase `ImprovedMiningScheduler`:

- **Genera 50 tareas** organizadas en 8 fases de un proyecto tipo (Preparación del Terreno → Movimiento de Tierra → Cimentaciones → Estructuras Principales → Instalaciones Mecánicas → Instalaciones Eléctricas → Acabados y Pruebas → Puesta en Marcha). Cada tarea tiene duración, costo base y fechas planificadas.
- **Red de dependencias realista** con los 4 tipos de precedencia estándar (más *lag* positivo o negativo):
  - `FS` Finish-to-Start: la sucesora no puede empezar hasta que la predecesora termine (la más común).
  - `SS` Start-to-Start: pueden comenzar juntas.
  - `FF` Finish-to-Finish: deben terminar juntas.
  - `SF` Start-to-Finish: el inicio de la predecesora controla el fin de la sucesora.

  Incluye detección y eliminación de ciclos (DFS) y cálculo de fechas de inicio/fin propagando las restricciones de los predecesores.
- **Perfiles de proyecto aleatorios** por simulación: Adelantado (60–80 % completado), Normal (40–60 %), Retrasado (20–40 %), Inicial (5–20 %) y Crítico (30–45 %), combinados con estilos de red (Paralela, Secuencial, Mixta, Compleja) y estrategias de buffer (conservative, moderate, aggressive).
- **Estados coherentes**: una tarea solo puede estar completada o en progreso si sus predecesores lo permiten según el tipo de dependencia.
- **Cálculos automáticos** por tarea: días de retraso, buffer sugerido (según riesgo de la fase, estrategia y complejidad), % de avance físico, costo real y sobrecosto, causa de retraso.
- **Salidas**:
  - Excel con formato (hoja `Cronograma` + hoja `Resumen` con métricas).
  - Gantt interactivo (planificado vs. real, buffers y línea de "hoy").
  - Diagrama de red de dependencias coloreado por fase y tipo de relación.
  - Dashboard comparativo entre simulaciones.
  - Métricas y reportes por consola (`generate_summary_metrics`, `generate_dependency_report`, `print_schedule_summary`).

## Instalación

Requiere Python 3.9+.

```bash
git clone https://github.com/Snickmax/SimulAdapvector.git
cd SimulAdapvector
python -m venv .venv
source .venv/bin/activate        # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## Ejecución

Ejecución completa (3 simulaciones + Excel + visualizaciones en el navegador):

```bash
python simul.py
```

Uso como módulo, con control fino:

```python
from simul import ImprovedMiningScheduler, generate_multiple_simulations, quick_test

# Una simulación
scheduler = ImprovedMiningScheduler()      # acepta project_start_date, current_date, simulation_id
scheduler.generate_coherent_tasks()

df = scheduler.create_dataframe()          # cronograma como pandas.DataFrame
scheduler.print_schedule_summary()         # resumen por consola
scheduler.export_to_excel("cronograma.xlsx")

scheduler.create_enhanced_gantt().show()   # Gantt interactivo (Plotly)
scheduler.create_network_diagram().show()  # diagrama de red de dependencias

# Varias simulaciones comparables
sims = generate_multiple_simulations(num_simulations=3)

# Prueba rápida de que todo funciona
quick_test()
```

## Formato de entrada y salida

**Entrada:** no requiere archivos de entrada. Los datos se generan internamente; los parámetros opcionales del constructor son:

| Parámetro | Tipo | Descripción |
|---|---|---|
| `project_start_date` | `datetime` | Inicio del proyecto (por defecto: 180–365 días antes de hoy, aleatorio) |
| `current_date` | `datetime` | Fecha de evaluación "hoy" (por defecto: `datetime.now()`) |
| `simulation_id` | `str` | Identificador (por defecto: `SIM-XXXX` aleatorio) |

**Salida principal:** un `pandas.DataFrame` (y su Excel `cronograma_<ID>.xlsx`) con una fila por tarea y estas columnas:

`ID`, `Fase`, `Tarea`, `Duración Planificada (días)`, `Inicio Planificado`, `Fin Planificado`, `Predecesor` (IDs separados por coma), `Costo Planificado (USD)`, `Riesgo de Retraso (%)`, `Estado` (Completada / Completada con retraso / Completada anticipadamente / En progreso / En progreso (con retraso) / En progreso (adelantada) / No iniciada), `Inicio Real`, `Fin Real`, `Duración Real (días)`, `% Avance Físico`, `Costo Real (USD)`, `Retraso (días)`, `Sobrecosto (USD)`, `Causa de Retraso`, `Observaciones`, `Días de Retraso`, `Buffer sugerido (días)`.

El Excel incluye además una hoja `Resumen` con métricas globales (avance promedio, tareas con retraso, buffers, presupuesto vs. gastado, estadísticas de dependencias, etc.). La columna interna `Predecesores Detallados` (tuplas `(id, tipo, lag)`) se usa para las visualizaciones y se excluye del Excel.

## Integración con Adapvector

Este simulador está pensado para integrarse como **módulo de cronogramas de [Adapvector](https://github.com/Snickmax/Adapvector)**: genera los datos de cronograma (tareas, dependencias, avances, retrasos y buffers) que Adapvector consumirá para sus funcionalidades de planificación y análisis predictivo de proyectos, sirviendo mientras tanto como fuente de datos sintéticos para desarrollo y pruebas.

## Mejoras futuras

- Implementar el algoritmo CPM completo (forward/backward pass) con cálculo real de holguras y camino crítico.
- Usar distribuciones probabilísticas validadas para el modelado de riesgos.
- Validación más robusta de coherencia lógica y alineación con estándares de la industria (PMI).
- Modelado de recursos y nivelación.
