## NOTERING FÖR SETUP
Behöver skapa en secrets.h och lägga in i projektet.
```c
// secrets.h
#ifndef SECRETS_H
#define SECRETS_H

// Wi-Fi credentials
const char* WIFI_SSID = "name here";
const char* WIFI_PASSWORD = "pass here";
const char* THINGSPEAK_API_KEY = "api write key here";

#endif
```
![image](https://github.com/user-attachments/assets/9076e793-782a-4a77-bf2f-2e100aa33691)

- [Public view of temperature data](https://thingspeak.mathworks.com/channels/2718779/charts/1?bgcolor=%23ffffff&color=%23d62020&dynamic=true&results=60&type=line&update=15)
- [Public view of humidity data](https://thingspeak.mathworks.com/channels/2718779/charts/2?bgcolor=%23ffffff&color=%23d62020&dynamic=true&results=60&type=line&update=15)
# **Systemspecificering för IoT-baserad temperatur- och fuktighetsövervakning**

## 1. **Inledning**

Denna Proof of Concept (PoC) beskriver en IoT-lösning för att övervaka temperatur och luftfuktighet i en miljö. Kunden har efterfrågat en enkel och pålitlig metod för att fjärrövervaka dessa parametrar med säker kommunikation och enkel integration med befintliga system. Lösningen bygger på användning av en **ESP32-mikrokontroller** kopplad till en **DHT11-sensor** för mätning av temperatur och fuktighet. Data skickas över Wi-Fi till **ThingSpeak-plattformen** för lagring och visualisering. Säkerhetsfunktioner såsom SSL/TLS-kryptering och korrekt certifikathantering är implementerade för att säkerställa säker och pålitlig dataöverföring.

PoC:n är utformad för att visa hur systemet fungerar i praktiken, med fokus på att etablera en säker HTTPS-anslutning mellan enheten och ThingSpeak-servern, samt att hantera eventuella problem som kan uppstå i denna process.

## 2. **Systemarkitektur**

### **2.1 Enhetsarkitektur**

Enheten som används för övervakning består av:

- **ESP32-mikrokontroller**: Hanterar sensoravläsning, Wi-Fi-anslutning och dataöverföring.
- **DHT11-sensor**: Mäter temperatur och luftfuktighet i miljön.
- **Firmware**: Utvecklad med Arduino-ramverket, ansvarar för att läsa sensordata och skicka den till ThingSpeak över en säker HTTPS-anslutning.

### **2.2 Backend**

Backend-systemet består av:

- **ThingSpeak-plattformen**: Används för att ta emot, lagra och visualisera sensordata som skickas från enheten.
- **MATLAB Analytics (valfritt)**: Kan användas för avancerad dataanalys och bearbetning av insamlade data.

### **2.3 Kommunikationsprotokoll**

Systemet använder följande protokoll och standarder:

- **Wi-Fi (802.11 b/g/n)**: För trådlös anslutning till internet.
- **HTTPS (SSL/TLS)**: För säker kommunikation mellan enheten och ThingSpeak-servern, med användning av korrekt rotcertifikat för serververifiering.

### **2.4 Säkerhet**

Säkerhet är en viktig komponent i systemet:

- **SSL/TLS-kryptering**: Används för att säkra kommunikationen mellan ESP32-enheten och ThingSpeak-servern. Rotcertifikatet för ThingSpeak (DigiCert Global Root G2) är korrekt implementerat i enhetens firmware för att möjliggöra certifikatverifiering.
- **Certifikathantering**: Certifikatet är korrekt formaterat och lagrat i firmware, med hänsyn till minnesbegränsningar och stränghantering i ESP32.
- **Uppdaterbar firmware**: Möjlighet att uppdatera enhetens firmware vid behov för att hantera säkerhetsuppdateringar eller förbättringar.

### **2.5 Energiförbrukning**

Eftersom enheten förväntas vara i kontinuerlig drift:

- **Strömsparlägen**: ESP32 kan konfigureras för att använda strömsparlägen mellan mätningar för att minska energiförbrukningen.
- **Optimerad kod**: Firmware är skriven för att minimera energiförbrukning genom effektiva kodstrukturer och undvika onödig bearbetning.

## 3. **Implementering**

### **3.1 Firmwareutveckling**

- **Programmeringsspråk**: C++ med Arduino-ramverket.
- **Bibliotek**:
  - **WiFi.h**: För Wi-Fi-anslutning.
  - **WiFiClientSecure.h**: För att hantera säker HTTPS-kommunikation.
  - **DHT.h**: För att interagera med DHT11-sensorn.

### **3.2 Certifikathantering**

- **Rotcertifikat**: DigiCert Global Root G2-certifikatet är inkluderat i firmware med korrekt formatering.
- **Stränghantering**: Certifikatet lagras som en konstant sträng med rätt syntax för att undvika fel vid kompilering eller körning.
- **Certifikatverifiering**: ESP32-enheten verifierar ThingSpeaks servercertifikat vid anslutning för att säkerställa att data skickas till rätt server.

### **3.3 Dataöverföring**

- **HTTP POST-förfrågan**: Sensordata skickas till ThingSpeak via en HTTPS POST-förfrågan.
- **Frekvens**: Data skickas med ett intervall som respekterar ThingSpeaks gränser för datafrekvens (minst 15 sekunders intervall för gratis konton).
- **Felhantering**: Firmware inkluderar logik för att hantera misslyckade anslutningar eller dataöverföringar, inklusive återförsök och felmeddelanden.

## 4. **Säkerhet och Överensstämmelse**

### **4.1 SSL/TLS**

- **Säker anslutning**: Använder SSL/TLS för att kryptera dataöverföringen.
- **Certifikatverifiering**: Implementerar korrekt certifikatverifiering för att förhindra man-in-the-middle-attacker.

### **4.2 Uppdateringar och Underhåll**

- **Firmwareuppdateringar**: Möjlighet att uppdatera firmware för att åtgärda säkerhetsproblem eller lägga till funktionalitet.
- **Säkerhetsövervakning**: Regelbunden kontroll av bibliotek och beroenden för kända sårbarheter (CVEs).

## 5. **Skalbarhet och Framtida Utveckling**

- **Utökning av sensorer**: Systemet kan utökas för att inkludera fler sensorer eller mätpunkter.
- **Integrering med andra plattformar**: Möjlighet att integrera med andra IoT-plattformar eller molntjänster.
- **Energioptimering**: Ytterligare optimeringar för att minska energiförbrukningen och förlänga batteritiden om enheten drivs på batteri.

## 6. **Hosting och Certifikat**

- **ThingSpeak Hosting**: Använder ThingSpeaks molnbaserade tjänst för datalagring och visualisering.
- **Certifikathantering**: Uppdatering av rotcertifikatet vid behov för att säkerställa fortsatt säker kommunikation.

## 7. **Möjliga framtida säkerhetsåtgärder**

För att säkerställa fullständig efterlevnad av **Cyber Resilience Act** och ytterligare höja systemets säkerhet kan följande åtgärder implementeras:

### **7.1 Secure Boot**

Implementera **Secure Boot** för att säkerställa att enheten endast kör signerad och betrodd firmware. Detta skulle förhindra att skadlig kod körs på enheten.

### **7.2 Firmware Over The Air (FOTA)**

Stöd för **FOTA** kan implementeras för att möjliggöra fjärruppdatering av firmware. Detta skulle säkerställa att systemet kan uppdateras med säkerhetsfixar utan att kräva fysisk åtkomst till enheterna. En rollback-funktion bör inkluderas för att säkerställa att enheten återgår till en fungerande version om en uppdatering misslyckas.

### **7.3 Mutual TLS (mTLS)**

Införa **mutual TLS (mTLS)** för att autentisera både klienten och servern. Detta skulle förstärka säkerheten ytterligare och säkerställa att endast betrodda enheter kan ansluta till ThingSpeak eller liknande backend-tjänster.

### **7.4 API-nycklar och autentisering**

Använd **API-nycklar** eller tokens för autentisering mot ThingSpeak, för att förstärka åtkomstkontrollen och säkerställa att endast behöriga enheter kan skicka data.

### **7.5 Intrångsdetektering**

Införa en metod för **intrångsdetektering** som kan övervaka och rapportera potentiellt skadlig aktivitet, till exempel oväntade dataflöden eller frekventa felaktiga anslutningar.

### **7.6 Sårbarhetsskanning och regelbundna uppdateringar**

Implementera ett system för **regelbundna säkerhetsskanningar och uppdateringar** av systemets bibliotek och beroenden för att snabbt åtgärda nya säkerhetssårbarheter.

## 8. **Sammanfattning**

Denna PoC visar en fungerande lösning för IoT-baserad temperatur- och fuktighetsövervakning med säker dataöverföring till ThingSpeak. Genom att följa bästa praxis för säkerhet och effektiv kodning uppfyller systemet kraven på pålitlighet och säkerhet
