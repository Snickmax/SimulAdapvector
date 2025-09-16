# SimulAdapvector
Descripción General del Código de Simulación de Cronograma 


Proyecto PredictSys – Parte 1: Simulación de Cronograma



Descripción del Codigo
•	Permite generar cronogramas realistas con dependencias, estados de avance, visualizaciones y métricas automáticas para mejorar la planificación, el seguimiento y la toma de decisiones en proyectos mineros.

Componentes del sistema (Principales)
•	Crea 50 tareas organizadas.
•	Cada tarea tiene duración, costo, y fechas planificadas.
•	Asigna estados realistas: completadas, en progreso, o no iniciadas

Sistema de Dependencias
       Establece relaciones lógicas entre tareas (una no puede empezar hasta que otra termine)
•	Usa 4 tipos de relaciones: 

FS: Finish-to-Start (la más común)	una tarea no puede empezar hasta que la anterior termine
SS: Start-to-Start (pueden empezar juntas)	dos tareas pueden comenzar al mismo tiempo.
FF: Finish-to-Finish (deben terminar juntas)	dos tareas deben terminar juntas.
SF: Start-to-Finish (menos común)	el inicio de la predecesora controla el fin de la sucesora"

 Genera diferentes escenarios de proyecto:

•	Proyecto adelantado (60-80% completado)
•	Proyecto normal (40-60% completado)
•	Proyecto retrasado (20-40% completado)
•	Proyecto inicial (5-20% completado)
•	Proyecto crítico (30-45% completado)

Cálculos Automáticos
Días de retraso	Cuánto se ha atrasado cada tarea
Buffers sugeridos	Tiempo extra recomendado por riesgos
Fechas realista	Basadas en dependencias y duraciones

Características realistas:
•	Dependencias complejas: Las tareas siguen un orden lógico
•	Estados coherentes: No puede haber tareas completadas si sus predecesores no están listos.
•	Cálculo de buffers: Tiempo extra basado en riesgos de cada fase.
•	Múltiples escenarios: Diferentes perfiles de avance del proyecto. 
•	Métricas automáticas: Resúmenes y estadísticas del proyecto
.





*Puntos que se está evaluando según CHATGPT para mejorar el código técnicamente*
Para Hacer el Código Técnicamente Sólido:
1.	Implementar algoritmo CPM completo con forward/backward pass
2.	Agregar cálculo real de float y camino crítico
3.	Usar distribuciones probabilísticas validadas para riesgos
4.	Implementar validación robusta de coherencia lógica
5.	Seguir estándares de la industria (PMI, etc.)
6.	Agregar modelado de recursos y nivelación
 

