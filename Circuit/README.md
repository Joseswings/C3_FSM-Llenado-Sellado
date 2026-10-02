# Arquitectura del Circuito (Logisim-Evolution)

Esta carpeta contiene el archivo fuente `.circ` (`C3_top_JoseAlas.circ`) con la simulación completa del mecanismo de llenado y sellado. 

El diseño del circuito se implementó siguiendo una **arquitectura estrictamente jerárquica y modular**, dividiendo el sistema en tres niveles de abstracción para facilitar su lectura, depuración y escalabilidad.

## Distribución de los Subcircuitos

### Nivel 1: Lógica Combinacional Pura
Son "cajas negras" que no contienen memoria, únicamente compuertas lógicas (AND, OR, NOT) generadas a partir de las ecuaciones booleanas minimizadas (SOP).
*   `fsm1_next_state` y `fsm1_output`: Calculan el próximo estado y los actuadores de la FSM principal.
*   `fsm2_next_state` y `fsm2_output`: Calculan el próximo estado y las salidas de la válvula.

### Nivel 2: Bloques Secuenciales (FSMs)
Estos circuitos encapsulan los bloques del Nivel 1 y les agregan los **Flip-Flops D** y la red de sincronización (Reloj).
*   `fsm1_block`: Controlador Principal (Arquitectura Moore). Retroalimenta sus 2 bits de estado (`S1`, `S0`).
*   `fsm2_block`: Controlador de Válvula (Arquitectura Mealy). Retroalimenta su bit de estado (`V0`) e ingresa los sensores directamente a la lógica de salida para lograr una reacción inmediata.

### Nivel 3: Top-Level (Sistema Completo)
Es el circuito principal o *Main* y *TOP*(más simplificado aún). Aquí se instancian `fsm1_block` y `fsm2_block` interconectándolos entre sí (factorización). 
*   **Interconexión interna:** La señal `Enable_Fill` viaja de FSM 1 a FSM 2, y `Fill_Done` regresa de FSM 2 a FSM 1.
*   **Mundo Físico:** Aquí se encuentran los pines de entrada interactivos (botones y sensores de la máquina) y los pines de salida (motor, actuador térmico, válvula).