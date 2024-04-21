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

## Segunda parte del ejercicio práctico
```c++
#include <Arduino.h>
#include <FreeRTOS.h>
#include <task.h>
#include <semphr.h> 

// Definir el pin del LED
const int ledPin = 11;

// Crear un semáforo
SemaphoreHandle_t semaphore;

void setup() {
    Serial.begin(115200);
    // Inicializar el pin del LED como salida
    pinMode(ledPin, OUTPUT);

    // Crear un semáforo
    semaphore = xSemaphoreCreateBinary();
    
    // Crear tareas
    xTaskCreate(tareaEncender, "Encender LED", 1000, NULL, 1, NULL);
    xTaskCreate(tareaApagar, "Apagar LED", 1000, NULL, 1, NULL);
}

void loop() {
    // No hay código en el loop
}

// Tarea para encender el LED
void EncenderT1(void *parameter) {
    for (;;) {
        // Esperar a que el semáforo esté disponible
        xSemaphoreTake(semaphore, portMAX_DELAY);

        // Encender el LED
        digitalWrite(ledPin, HIGH);
        Serial.println("LED HIGH");
        delay(1000);
    }
}

// Tarea para apagar el LED
void ApagarT2(void *parameter) {
    for (;;) {
        xSemaphoreTake(semaphore, portMAX_DELAY);

        digitalWrite(ledPin, LOW);
        Serial.println("LED LOW");
        delay(1000);
    }
}
```

