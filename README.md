#include <Arduino.h>
//Definimos los pines en los cuales estarán conectados nuestros leds y switches
//Se pone LED1 o switch1 para instanciar los pines en vez de poner simplemente 7 o 9

#define LED1 7
#define LED2 6
#define LED3 5
#define LED4 4

#define switch1 9
#define switch2 10
#define switch3 11

bool ledStates[4] = {false, false, false, false}; //Define un arreglo de 4 leds y los inicializa en false(apagado)

bool switchState = false; // Estado para el switch3 que se utilizara posteriormente

unsigned long previousMillis = 0; // Variable para manejar el tiempo en lugar del delay

int tiempos[] = {2000, 1000, 500}; // tiempos para la secuencia especial en 2,1 y o.5 segundos

int ciclo = 0; // se utiliza para cumplir con los ciclos en la secuencia especial

int etapa = 0; // es un entero que nos dice en que parte de la secuencia especial esta el código

bool ledsEncendidos = false; // variable booleana que nos dice el estado de los leds

//configuración de los pines como entradas o salidas
void setup() {
    pinMode(LED1, OUTPUT);
    pinMode(LED2, OUTPUT);
    pinMode(LED3, OUTPUT);
    pinMode(LED4, OUTPUT);

    pinMode(switch1, INPUT);
    pinMode(switch2, INPUT);
    pinMode(switch3, INPUT);

    Serial.begin(9600); // es la configuración para ver los mensajes en el monitor serial
}

void actualizarLEDs() {
  digitalWrite(LED1, ledStates[0]);
  digitalWrite(LED2, ledStates[1]);
  digitalWrite(LED3, ledStates[2]);
  digitalWrite(LED4, ledStates[3]);
} // Actualiza el estado de los leds y lo almacena en el array de ledStates[] puede ser true para encendido o false para apagado dado que bool ledStates[4] es booleano 

// Ahora haremos el código mas complicado que seria para la secuencia especial
void secuenciaEspecial() {
unsigned long currentMillis = millis(); //utilizamos millis() en vez de delay()

 if (currentMillis - previousMillis >= tiempos[etapa]) {
    previousMillis = currentMillis;

     // Alternar entre encender y apagar LEDs
     ledsEncendidos = !ledsEncendidos;

     int j = 0;
    while (j < 4) {  
        ledStates[j] = ledsEncendidos;
         j++;
     }
     actualizarLEDs(); // se utiliza para los cambios de los leds en sus pines fisicos

     // Verificar si se completaron 3 ciclos en la etapa actual
    if (!ledsEncendidos) { 
        ciclo++;
         if (ciclo >= 3) {
          ciclo = 0;
          etapa++;
          if (etapa >= 3) {
              etapa = 0;  //Reiniciar Secuencia
        }
      }
    }
  }
}

void loop() {
    switchState = digitalRead(switch3); //Guarda el estado del switch 3
  Serial.print("switch1: "); //imprime con 1 o 0 el estado del switch1
  Serial.print(digitalRead(switch1)); 
  Serial.print(" switch2: "); //imprime con 1 o 0 el estado del switch2
  Serial.print(digitalRead(switch2));
  Serial.print(" switch3: "); //imprime con 1 o 0 el estado del switch3
  Serial.println(switchState);

switch (switchState) {
     case HIGH:  // Si el switch3 está activado, ejecuta la secuencia especial
     secuenciaEspecial();
     break;
        
     case LOW:  // Si switch3 está apagado, usar switch1 y switch2 para controlar los LED´s 1 y 2
      ledStates[0] = digitalRead(switch1);
      ledStates[1] = digitalRead(switch2);
      ledStates[2] = false;
      ledStates[3] = false;
      actualizarLEDs();
     break;
  }
}
