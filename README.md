# Práctica 4: SISTEMAS OPERATIVOS EN TIEMPO REAL
## Introducción de la práctica
En esta práctica va enfocada en los sistemas operativos en tiempo real, especialmente la ejecución de tareas, dónde se dividirán entre ellas el tiempo de uso para realizarlas.

## Primera parte del ejercicio práctico
```c++
#include<Arduino.h>

void anotherTask(void *parameter);

void setup() {
  Serial.begin(112500);

  xTaskCreate(
      anotherTask,     
      "another Task",  
      10000,           
      NULL,             
      1,               
      NULL              
  );
}

void loop() {
  Serial.println("this is ESP32 Task");
  delay(1000);
}

void anotherTask(void *parameter) {
  
  for (;;) {
    Serial.println("this is another Task");
    delay(1000);
  }

  vTaskDelete(NULL);
}
```
El funcionamiento de el código proporcionado, se basa en que crea 2 tareas donde se utliza un un sistema de operativo de tiempo real FreeRTOS.

- **La tarea principal**: se ejecuta en la función *loop()*, imprime repetidamente un mensaje ("this is ESP32 Task") en el puerto serie y espera 1 segundo entre cada impresión.
- **La segunda tarea**: creada en la función *setup()* y llamada anotherTask, también imprime repetidamente un mensaje ("this is another Task") en el puerto serie y espera 1 segundo entre cada impresión.

Las salidas que se muestran ppor el puerto serie son las siguientes:
```
   - this is ESP32 Task
   - this is another Task
```
Adjunto diagrama de flujo del funcionamiento:
```mermaid
graph TD;
    A[Inicio] --> B[Serial Inicialización]
    B --> C[Creación de tarea]
    C --> D[Bucle principal (loop())]
    D --> E[Tarea secundaria (anotherTask)]
    E --> D
    E --> F[Eliminación de tarea]
```
## Segunda parte del ejercicio práctico
```c++
#include <Arduino.h>
long debouncing_time = 150; 
volatile unsigned long last_micros;
 
SemaphoreHandle_t interruptSemaphore;
void interruptHandler() {
  xSemaphoreGiveFromISR(interruptSemaphore, NULL);
}
 
void TaskLed(void *pvParameters)
{
  (void) pvParameters;
  pinMode(8, OUTPUT);
  for (;;) {
    if (xSemaphoreTake(interruptSemaphore, portMAX_DELAY) == pdPASS) {
      digitalWrite(8, !digitalRead(8));
    }
  }
}
void TaskBlink(void *pvParameters)
{
  (void) pvParameters;
  pinMode(7, OUTPUT);
  for (;;) {
      digitalWrite(7, HIGH);
      vTaskDelay(200 / portTICK_PERIOD_MS);
      digitalWrite(7, LOW);
      vTaskDelay(200 / portTICK_PERIOD_MS);
  }
}
void debounceInterrupt() {
  if((long)(micros() - last_micros) >= debouncing_time * 1000) {
    interruptHandler();
    last_micros = micros();
  }
}
```

