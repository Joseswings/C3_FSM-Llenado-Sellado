# C3_FSM-Llenado-Sellado
# Mecanismo de Llenado y Sellado de Envases (FSM Factorizada)

Este repositorio contiene el diseño, simulación y documentación de una Máquina de Estados Finitos (FSM) que controla una línea de embotellado. El proyecto implementa una **arquitectura factorizada** dividida en dos submáquinas interactuantes, aplicando los principios de diseño digital (Moore y Mealy).

## Arquitectura del Sistema

El sistema fue modelado siguiendo un enfoque jerárquico y se divide en dos bloques principales:

1. **FSM 1: Controlador Principal (Arquitectura Moore)**
   * **Función:** Controla el avance de la banda transportadora, la etapa de llenado y el actuador térmico de sellado.
   * **Justificación:** Las salidas dependen únicamente del estado actual, garantizando estabilidad en los tiempos de las etapas mecánicas.

2. **FSM 2: Controlador de Válvula Rápida (Arquitectura Mealy)**
   * **Función:** Controla la apertura y cierre de la boquilla de líquido.
   * **Justificación:** Se eligió Mealy para permitir que la válvula reaccione de forma **inmediata** (asíncrona al estado siguiente) en el instante en que el sensor de nivel detecta que el envase está lleno, previniendo derrames sin esperar un ciclo de reloj adicional.

## Variables del Sistema

* **Entradas (Sensores):** `ON_OFF`, `Sens_Envase`, `Sens_Nivel`, `Sens_Sello`
* **Salidas (Actuadores):** `Motor_Cinta`, `Actuador_Sello`, `Valvula`
* **Interconexión (Factorización):** `Enable_Fill` (FSM 1 -> FSM 2), `Fill_Done` (FSM 2 -> FSM 1)

## Archivos del Repositorio

* `Mecanismo_Llenado_Sellado.circ`: Archivo fuente simulable en **Logisim Evolution**.
* `Tablas_FSM_Llenado.xlsx`: Tablas de verdad, transiciones, mapas lógicos y ecuaciones booleanas minimizadas (SOP).
* [Enlace al Video Explicativo](https://youtube.com/tu_enlace_aqui)

## Instrucciones de Simulación

1. Abrir el archivo `.circ` en Logisim Evolution.
2. Navegar al circuito Top-Level.
3. Usar la herramienta *Poke Tool* (Mano) para encender `ON_OFF` (1).
4. Alternar los sensores lógicos de entrada y presionar manualmente el componente de Reloj (Clock) para observar el flujo de estados y la comunicación entre ambas máquinas.
