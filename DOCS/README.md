# Documentación Lógica: Tablas y Ecuaciones

En esta carpeta se encuentra el archivo principal de Excel (`C3_Jose_Alas.xlsx`) que contiene el diseño matemático y lógico de la Máquina de Estados Finitos (FSM) factorizada.

## 1. Tablas de Verdad y Transición
Para cada submáquina, se construyeron tablas que dictan su comportamiento:
*   **Tabla de Transición de Estados:** Mapea el *Estado Actual* y las *Entradas* para determinar cuál será el *Próximo Estado*. Se utilizaron "Don't Cares" (X) para aquellas entradas que no afectan la decisión en un estado particular, simplificando así la lógica.
*   **Tabla de Salidas:** Determina el valor de los actuadores. En la **FSM 1 (Moore)**, las salidas dependen exclusivamente del estado actual. En la **FSM 2 (Mealy)**, la tabla es combinada, ya que las salidas reaccionan instantáneamente a las entradas (ej. cierre rápido de la válvula).

## 2. Origen de las Ecuaciones Booleanas
Las ecuaciones se extrajeron directamente de las tablas utilizando el método de **Suma de Productos (SOP)**. 
1. Se identificaron todas las filas donde el bit del *Próximo Estado* o la *Salida* es igual a `1`.
2. Se escribieron los minitérminos correspondientes a esas filas.
3. Se agruparon y minimizaron utilizando álgebra booleana para reducir la cantidad de compuertas lógicas necesarias.

### Ecuaciones Finales Implementadas:
**FSM 1 (Moore - Controlador Principal)**
*   `S1_next = O & (~S1 & S0 & E | S1 & ~S0 | S1 & S0 & ~S)`
*   `S0_next = O & (~S1 & ~S0 | ~S1 & S0 & ~E | S1 & ~S0 & F | S1 & S0)`
*   `Motor_Cinta = ~S1 & S0`
*   `Enable_Fill = S1 & ~S0`
*   `Actuador_Sello = S1 & S0`

**FSM 2 (Mealy - Controlador de Válvula)**
*   `V0_next = EN & ~N`
*   `Valvula = EN & ~N`
*   `Fill_Done = V0 & N`