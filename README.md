# **Práctica 6: Buses de Comunicación**
Participants: Alexandre Pascual / Marti Vila

## **1. Introducción**
En la práctica anterior, se exploraron los buses de comunicación, centándose en la comunicación I2C. En esta práctica, se continuará con dicho estudio, enfocándose en el bus SPI (Serial Peripheral Interface).

Los principales objetivos de esta práctica son:
- Implementar la lectura y escritura de una memoria SD utilizando SPI.
- Leer datos desde una etiqueta RFID mediante el protocolo SPI.

## **2. Desarrollo de la Práctica**
### **Ejercicio 1: Lectura y escritura en memoria SD**

A continuación, se presenta el código utilizado para la lectura de un archivo de texto desde una tarjeta SD:

```c++
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
File myFile;
void setup()
{
 Serial.begin(9600);
 Serial.print("Iniciando SD ...");
 if (!SD.begin(10)) {
 Serial.println("No se pudo inicializar");
 return;
 }
 Serial.println("inicializacion exitosa");
 myFile = SD.open("/archivo.txt");//abrimos el archivo
 if (myFile) {
 Serial.println("archivo.txt:");
 while (myFile.available()) {
 Serial.write(myFile.read());
 }
 myFile.close(); //cerramos el archivo
 } else {
 Serial.println("Error al abrir el archivo");
 }
}
void loop()
{

}
```

### **Funcionamiento del código**
El programa inicializa la comunicación con una tarjeta SD y abre un archivo de texto llamado `archivo.txt`. Si la inicialización es exitosa, el programa procede a leer el contenido del archivo y lo muestra en el monitor serie. Finalmente, se cierra el archivo.

### **Salida esperada por el monitor serie:**
```
Iniciando SD ... inicializacion exitosa
archivo.txt:
Soy el mejor del mundo
```

![SD](https://github.com/user-attachments/assets/895412b4-20b4-4887-8a45-959b0d6397d1)


### **Ejercicio 2: Lectura de una etiqueta RFID**

En este ejercicio, se configura un lector RFID para identificar tarjetas y obtener su UID.

```c++
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 0 //Pin 9 para el reset del RC522
#define SS_PIN 10 //Pin 10 para el SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); //Creamos el objeto para el RC522
void setup() {
Serial.begin(9600); //Iniciamos la comunicación serial
SPI.begin(); //Iniciamos el Bus SPI
mfrc522.PCD_Init(); // Iniciamos el MFRC522
Serial.println("Lectura del UID");
}
void loop() {
// Revisamos si hay nuevas tarjetas presentes
if ( mfrc522.PICC_IsNewCardPresent())
 {
 //Seleccionamos una tarjeta
 if ( mfrc522.PICC_ReadCardSerial())
 {
 // Enviamos serialemente su UID
 Serial.print("Card UID:");
 for (byte i = 0; i < mfrc522.uid.size; i++) {
  Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0"
    : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     }
     Serial.println();
     // Terminamos la lectura de la tarjeta actual
     mfrc522.PICC_HaltA();
     }
    }
    }
```


### **Funcionamiento del código**
El programa inicializa la comunicación SPI y configura el lector RFID MFRC522. Luego, constantemente revisa si hay nuevas tarjetas presentes. Cuando una tarjeta se acerca al lector, el programa lee su UID y lo muestra en el monitor serie.

### **Ejemplo de salida esperada en el monitor serie:**
De la tarjeta:
```
Lectura del UID
Card UID: 05 E7 F7 04
```
![SPICard](https://github.com/user-attachments/assets/a76bf756-7d58-453a-8a52-5084ffe1cfc3)
