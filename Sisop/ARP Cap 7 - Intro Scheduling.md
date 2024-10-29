# Scheduling: Introduction

> Una política de scheduling es un algoritmo que determina cuándo un proceso debe ser ejecutado por el CPU. El objetivo principal de un algoritmo de scheduling es maximizar la utilización del CPU y minimizar el tiempo de espera de los procesos.

## Simplificaciones del workload

> El **workload** es, como su nombre indica, la carga de trabajo de un sistema.

1. Todos los trabajos corren la misma cantidad de tiempo.
2. Todos los trabajos llegan al mismo tiempo.
3. Todos los trabajos corren hasta completarse.
4. No hay I/O.
5. Cuanto tarda un proceso en completarse es conocido.

> [!NOTE] 
> Esto no se cumple en un sistema real, son simplificaciones para introducir de forma sencilla algunas políticas de scheduling.

## Métricas de Scheduling

> $T_{\text{turn_around}} = T_{\text{completion}} - T_{\text{arrival}}$

