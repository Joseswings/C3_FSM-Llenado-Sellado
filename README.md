# C3_FSM
# Mecanismo de Llenado y Sellado de Envases

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

## Explicación de los Diagramas de Estados (FSM)

Para resolver este problema de manera estructurada, el sistema se ha factorizado en dos máquinas de estados que operan en conjunto:

### FSM 1: Controlador Principal (Arquitectura Moore)
Controla la secuencia general del proceso de la línea de ensamblaje. Al ser una arquitectura de Moore, las salidas del sistema se asocian directamente al estado en el que se encuentra la máquina, garantizando estabilidad en cada etapa mecánica.

![FSM1_Diagram](/IMG/FSM1_diagram.png)

**Estados (4 estados):**
* **`S0_IDLE` (Espera):** La máquina está detenida o apagada.
* **`S1_MOVER` (Mover Cinta):** La cinta avanza buscando un envase.
* **`S2_LLENAR` (Llenado):** Se detiene la cinta y manda la señal a la FSM 2 para que llene.
* **`S3_SELLAR` (Sellado):** Baja el actuador térmico para sellar la botella.

**Entradas (Inputs):**
* **`ON_OFF` (1 bit):** Botón general. Si es 0, la máquina se detiene; si es 1, la máquina opera.
* **`Sens_Envase` (1 bit):** Sensor óptico que da '1' cuando un envase vacío está exactamente posicionado bajo la boquilla de llenado.
* **`Sens_Nivel` (1 bit):** Sensor que da '1' en el instante en que el líquido llega al tope del envase.
* **`Sens_Sello` (1 bit):** Sensor de presión que da '1' cuando el mecanismo de sellado ha terminado de presionar la tapa.

**Salidas (Outputs):**
* **`Motor_Cinta` (1 bit):** Enciende (1) o apaga (0) la banda transportadora para mover los envases. *(Controlado por la FSM 1)*
* **`Actuador_Sello` (1 bit):** Baja (1) o sube (0) el pistón térmico que sella la botella. *(Controlado por la FSM 1)*
* **`Valvula` (1 bit):** Abre (1) o cierra (0) la boquilla de llenado de líquido. *(Controlado por la FSM 2)*

**Variables de Interconexión (Factorización entre la FSM 1 y la FSM 2):**
* **`Enable_Fill` (1 bit):** Señal que la FSM 1 (Controlador de Etapas) le envía a la FSM 2 (Válvula) para decirle: *"El envase está en posición, puedes empezar a llenar"*.
* **`Fill_Done` (1 bit):** Señal que la FSM 2 (Válvula) le devuelve a la FSM 1 para decirle: *"Ya terminé de llenar, cierra tu ciclo y pasa a sellar"*.

---

### FSM 2: Controlador de Válvula (Arquitectura Mealy)
Maneja exclusivamente el flujo de líquido. Al ser una arquitectura de Mealy, la salida depende tanto del estado actual como de la entrada directa del sensor de nivel. Esto permite que la válvula se cierre instantáneamente al detectar que el envase está lleno, sin tener que esperar un ciclo de reloj adicional.

![FSM2_Diagram](/IMG/FSM2_diagram.png)

**Estados (2 estados):**
* **`V0_CERRADA`:** La válvula está cerrada esperando órdenes de la FSM 1.
* **`V1_ABIERTA`:** La válvula está abierta vertiendo líquido.

## Archivos del Repositorio

## Archivos del Repositorio

El proyecto está organizado jerárquicamente para separar el diseño lógico de la implementación física. A continuación se detalla la estructura del repositorio:

```text
C3_FSM-Llenado-Sellado
├── README.md                 
├── 📁 Circuit/                  
│   ├── C3_top_JoseAlas.circ  
│   └── README.md             # Explicación de la arquitectura jerárquica del circuito
├── 📁 DOCS/                     
│   ├── C3_Jose_Alas.xlsx     
│   └── README.md             # Documentación de las ecuaciones booleanas minimizadas
└── 📁 IMG/                      
    ├── FSM1_diagram.png      
    └── FSM2_diagram.png      
```

* [Enlace al Video Explicativo](https://drive.google.com/file/d/1qRHV2ZT8w6uF4EsDcwYhbKOsGq08n9HE/view?usp=sharing)

## Instrucciones de Simulación

1. Abrir el archivo `.circ` en Logisim Evolution.
2. Navegar al circuito Top-Level.
3. Usar la herramienta *Poke Tool* (Mano) para encender `ON_OFF` (1).
4. Alternar los sensores lógicos de entrada y presionar manualmente el componente de Reloj (Clock) para observar el flujo de estados y la comunicación entre ambas máquinas.
