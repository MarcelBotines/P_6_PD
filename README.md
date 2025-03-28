# **Pràctica 6: Busos de Comunicació**

## **1. Introducció**
En la pràctica anterior, es van explorar els busos de comunicació, centrant-se en la comunicació I2C. En aquesta pràctica, es continuarà amb aquest estudi, enfocant-se en el bus SPI (Serial Peripheral Interface).

Els principals objectius d’aquesta pràctica són:
- Implementar la lectura i escriptura d’una memòria SD utilitzant SPI.
- Llegir dades des d’una etiqueta RFID mitjançant el protocol SPI.

## **2. Desenvolupament de la Pràctica**
### **Exercici 1: Lectura i escriptura en memòria SD**

A continuació, es presenta el codi utilitzat per a la lectura d’un fitxer de text des d’una targeta SD:

```c++
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>
File myFile;
void setup()
{
 Serial.begin(9600);
 Serial.print("Iniciant SD ...");
 if (!SD.begin(10)) {
 Serial.println("No s'ha pogut inicialitzar");
 return;
 }
 Serial.println("Inicialització exitosa");
 myFile = SD.open("/fitxer.txt"); // obrim el fitxer
 if (myFile) {
 Serial.println("fitxer.txt:");
 while (myFile.available()) {
 Serial.write(myFile.read());
 }
 myFile.close(); // tanquem el fitxer
 } else {
 Serial.println("Error en obrir el fitxer");
 }
}
void loop()
{

}
```

### **Funcionament del codi**
El programa inicialitza la comunicació amb una targeta SD i obre un fitxer de text anomenat `fitxer.txt`. Si la inicialització és exitosa, el programa procedeix a llegir el contingut del fitxer i el mostra en el monitor sèrie. Finalment, es tanca el fitxer.

### **Sortida esperada pel monitor sèrie:**
```
Iniciant SD ... inicialització exitosa
fitxer.txt:
Soc el millor del món
```

![SD](https://github.com/user-attachments/assets/895412b4-20b4-4887-8a45-959b0d6397d1)

### **Exercici 2: Lectura d’una etiqueta RFID**

En aquest exercici, es configura un lector RFID per identificar targetes i obtenir el seu UID.

```c++
#include <SPI.h>
#include <MFRC522.h>
#define RST_PIN 0 // Pin 9 per al reset del RC522
#define SS_PIN 10 // Pin 10 per al SS (SDA) del RC522
MFRC522 mfrc522(SS_PIN, RST_PIN); // Creem l'objecte per al RC522
void setup() {
Serial.begin(9600); // Iniciem la comunicació sèrie
SPI.begin(); // Iniciem el Bus SPI
mfrc522.PCD_Init(); // Iniciem el MFRC522
Serial.println("Lectura de l'UID");
}
void loop() {
// Comprovem si hi ha noves targetes presents
if ( mfrc522.PICC_IsNewCardPresent())
 {
 // Seleccionem una targeta
 if ( mfrc522.PICC_ReadCardSerial())
 {
 // Enviem en sèrie el seu UID
 Serial.print("Card UID:");
 for (byte i = 0; i < mfrc522.uid.size; i++) {
  Serial.print(mfrc522.uid.uidByte[i] < 0x10 ? " 0"
    : " ");
     Serial.print(mfrc522.uid.uidByte[i], HEX);
     }
     Serial.println();
     // Finalitzem la lectura de la targeta actual
     mfrc522.PICC_HaltA();
     }
    }
    }
```

### **Funcionament del codi**
El programa inicialitza la comunicació SPI i configura el lector RFID MFRC522. Després, comprova constantment si hi ha noves targetes presents. Quan una targeta s'acosta al lector, el programa llegeix el seu UID i el mostra en el monitor sèrie.

### **Exemple de sortida esperada en el monitor sèrie:**
De la targeta:
```
Lectura de l'UID
Card UID: 05 E7 F7 04
```
![SPICard](https://github.com/user-attachments/assets/a76bf756-7d58-453a-8a52-5084ffe1cfc3)
