# **Pràctica 6: Comunicació amb Busos**

## **1. Objectiu**
En aquesta pràctica es continuarà explorant els busos de comunicació, amb un enfocament específic en el bus SPI (Serial Peripheral Interface). A diferència de la pràctica anterior, on es va treballar amb I2C, ara es realitzarà la lectura i escriptura d'una memòria SD i la identificació d'etiquetes RFID mitjançant SPI.

Els objectius són:
- Implementar la lectura i escriptura de fitxers en una targeta SD.
- Configurar un lector RFID per obtenir el UID d'una targeta.



### **Exercici 1**


```c++
#include <SPI.h>
#include <SD.h>
#include <Arduino.h>

File fitxer;

void setup() {
    Serial.begin(9600);
    Serial.print("Inicialitzant targeta SD...");
    
    if (!SD.begin(10)) {
        Serial.println("No s'ha pogut inicialitzar la SD");
        return;
    }
    Serial.println("SD inicialitzada correctament");
    
    fitxer = SD.open("/dades.txt");
    if (fitxer) {
        Serial.println("Contingut de dades.txt:");
        while (fitxer.available()) {
            Serial.write(fitxer.read());
        }
        fitxer.close();
    } else {
        Serial.println("No s'ha pogut obrir el fitxer");
    }
}

void loop() {
    // No es requereix codi en el loop
}
```

### **Explicació del codi**
Aquest programa estableix la comunicació amb la targeta SD i obre un fitxer de text anomenat `dades.txt`. Si la SD es detecta correctament, es llegeix el contingut i es mostra per pantalla. En acabar, es tanca el fitxer per evitar errors.

```
Inicialitzant targeta SD... SD inicialitzada correctament
Contingut de dades.txt:
Aquest és un exemple de text.
```

![Imatge SD](https://github.com/user-attachments/assets/895412b4-20b4-4887-8a45-959b0d6397d1)

### **Exercici 2**

Aquest exercici consisteix en la configuració d'un lector RFID per detectar targetes i obtenir el seu UID.

```c++
#include <SPI.h>
#include <MFRC522.h>

#define RST_PIN 9
#define SS_PIN 10

MFRC522 lector(SS_PIN, RST_PIN);

void setup() {
    Serial.begin(9600);
    SPI.begin();
    lector.PCD_Init();
    Serial.println("Esperant targeta...");
}

void loop() {
    if (lector.PICC_IsNewCardPresent() && lector.PICC_ReadCardSerial()) {
        Serial.print("UID de la targeta: ");
        for (byte i = 0; i < lector.uid.size; i++) {
            Serial.print(lector.uid.uidByte[i] < 0x10 ? " 0" : " ");
            Serial.print(lector.uid.uidByte[i], HEX);
        }
        Serial.println();
        lector.PICC_HaltA();
    }
}
```

### **Explicació del codi**
Aquest programa inicialitza el lector RFID MFRC522 i monitora la presència de targetes. Quan es detecta una targeta, es llegeix el seu UID i es mostra per pantalla.

### **Exemple de sortida esperada:**
```
Esperant targeta...
UID de la targeta: 05 E7 F7 04
```

![Imatge RFID](https://github.com/user-attachments/assets/a76bf756-7d58-453a-8a52-5084ffe1cfc3)
