# Práctica 4: Sistemas operativos en tiempo real

En esta práctica va enfocada en los sistemas operativos en tiempo real, especialmente la ejecución de tareas, dónde se dividirán entre ellas el tiempo de uso para realizarlas.

## Primera parte del ejercicio práctico
```c++
#include<Arduino.h>

void anotherTask(void *parameter);

void setup() {
  Serial.begin(112500);

  // Creamos una nueva tarea aquí
  xTaskCreate(
      anotherTask,     // Función de la tarea
      "another Task",  // Nombre de la tarea
      10000,            // Tamaño de la pila de la tarea
      NULL,             // Parámetro de la tarea
      1,                // Prioridad de la tarea
      NULL              // Manejador de la tarea para realizar un seguimiento de la tarea creada
  );
}

// La función loop() es invocada por el loopTask de Arduino ESP32
void loop() {
  Serial.println("this is ESP32 Task");
  delay(1000);
}

// Esta función se invocará cuando se haya creado la tarea adicional (anotherTask)
void anotherTask(void *parameter) {
  // Bucle infinito
  for (;;) {
    Serial.println("this is another Task");
    delay(1000);
  }

  // Eliminar la tarea al finalizar (esto nunca ocurrirá porque es un bucle infinito)
  vTaskDelete(NULL);
}
```
El funcionamiento de el código proporcionado, se basa --------------


Las salidas que se muestran ppor el puerto serie son las siguientes;
   - this is ESP32 Task
   - this is another Task
______

## Segunda parte del ejercicio práctico
