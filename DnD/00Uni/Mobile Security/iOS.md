# iOS

## **1. Allgemeine Merkmale des Betriebssystems**

### 1.1 Grundlagen

-   Basierend auf OS X
    
-   Keine freie Software
    

### 1.2 Installation von Apps

-   Ausschließlich über den App Store
    
-   Registrierung und digitale Signatur durch Entwickler erforderlich
    
-   Prüfung durch Apple vor Veröffentlichung
    
-   _Zusätzliche Information:_ Apps werden in einer Sandbox-Umgebung ausgeführt, um systemweite Eingriffe zu verhindern.
    

### 1.3 Sicherheitsmechanismen der Hardware

-   Boot-ROM mit Apple-Zertifikat für digitale Signaturprüfung
    
-   Codeausführung nur mit gültiger Signatur
    
-   AES-Hardware-Verschlüsselung
    
-   Einzigartige Geräte-ID in Hardware
    
-   _Zusätzliche Information:_ Secure Enclave zur sicheren Speicherung sensibler Daten wie Face ID und Touch ID.
    

## **2. Speicher- und Dateisicherheit**

### 2.1 Dateiverschlüsselung

-   Jede Datei erhält einen eigenen Schlüssel
    
-   Verschlüsselungsschlüssel im RAM gespeichert
    
-   Datei-Zugriff nur verschlüsselt für andere Programme
    

### 2.2 Dateimanagement

-   Löschen von Dateien durch Löschen des zugehörigen Schlüssels
    
-   Dateifreigabe zwischen Programmen durch Teilen der Schlüssel
    
-   _Zusätzliche Information:_ Speicherverschlüsselung erfolgt mit XTS-AES-128 für maximale Sicherheit.
    

## **3. Systemarchitektur**

### 3.1 Core OS (Darwin)

-   UNIX-Betriebssystem ohne GUI
    
-   Zuständig für Hardwarezugriff, Speicherverwaltung und Dateisystem
    

### 3.2 Core & Security Services

-   Systembibliotheken, API für iCloud, SQLite-Schnittstellen
    
-   Sicherheitsfunktionen:
    
    -   Data Protection
        
    -   Automatic Reference Counting
        
-   In-App-Käufe und Keychain-Speicherverwaltung
    
-   _Zusätzliche Information:_ Apple verwendet ASLR (Address Space Layout Randomization), um Exploits zu erschweren.
    

### 3.3 Media

-   Bibliotheken für Grafiken, Videos, Audio, AirPlay
    
-   _Zusätzliche Information:_ Unterstützung für HEVC und ProRAW für bessere Medienqualität.
    

### 3.4 Cocoa Touch

-   Entwicklerfreundliche Schnittstellen
    
-   Foundation: Basisklassen wie Strings und Arrays
    
-   UIKit:
    
    -   AppKit für UI-Elemente
        
    -   CoreData für Datenmanagement
        
-   _Zusätzliche Information:_ SwiftUI als moderne Alternative zu UIKit für plattformübergreifende Entwicklung.
    

## **4. App-Aufbau unter iOS**

### 4.1 Programmierung

-   Objective-C oder Swift
    
-   Installation als IPA-Datei
    

### 4.2 Bestandteile einer App

-   `Payload/Applikation.app`: Enthält Ressourcen, Bibliotheken und App-Code
    
-   `iTunesArtwork`: Icon für App Store
    
-   `iTunesMetadata.plist`: Metadaten (Entwicklername, Version)
    
-   _Zusätzliche Information:_ `Entitlements.plist` definiert spezielle Berechtigungen einer App.
    

### 4.3 Aufbau einer ausführbaren Datei (Mach-O)

-   Verschlüsselt bis zur Installation
    
-   Enthält:
    
    -   **Header** (Metadaten)
        
    -   **Load-Kommandos** (Speicher- und Symboltabellen-Infos)
        
    -   **Data-Segmente** (Code und Daten)
        

## **5. Dateischutz und Keychain**

### 5.1 Data Protection Levels (`NSFileProtection`)

-   **None**: Geringer Schutz, Verschlüsselung nur bei ausgeschaltetem Gerät
    
-   **CompleteUntilFirstUserAuthentication**: Schlüssel bleibt bis zum Ausschalten erhalten
    
-   **CompleteUnlessOpen**: Schlüssel wird gelöscht, sobald Datei geschlossen wird
    
-   **Complete**: Schlüssel wird gelöscht, sobald Bildschirm gesperrt wird
    

### 5.2 Keychain-Sicherheit

-   Speicherung hochsensibler Daten
    
-   API `kSecAttr` für Schutzlevel:
    
    -   **Always**: Immer verfügbar
        
    -   **AfterFirstUnlock**: Verfügbar nach erstem Entsperren
        
    -   **WhenUnlocked**: Nur verfügbar, wenn Gerät entsperrt ist
        
    -   **ThisDeviceOnly**-Varianten: Daten nicht auf andere Geräte übertragbar
        
-   _Zusätzliche Information:_ Secure Enclave schützt biometrische Daten zusätzlich.
    

## **6. Bootschutz – Secure Boot Chain**

### 6.1 Boot-Prozess

-   Boot-ROM → LLB → iBoot → iOS-Kernel
    
-   Alle Komponenten digital signiert und überprüft
    

### 6.2 Komponenten der Secure Boot Chain

-   **Boot ROM**: Enthält Public Key der Apple Root-CA
    
-   **LLB (Low Level Bootloader)**: Startet iBoot und überprüft Signatur
    
-   **iBoot**: Überprüft Signatur des iOS-Kernels
    
-   **iOS Kernel**: Startet das Betriebssystem
    
-   _Zusätzliche Information:_ SEP (Secure Enclave Processor) führt kritische Sicherheitsoperationen isoliert durch.
    

## **7. Bedrohungsszenarien**

### 7.1 Kategorien

-   **Webbasierte Bedrohungen**
    
    -   Schwachstellen in mobilen Browsern
        
    -   Schadlinks via E-Mail oder Social Media
        
-   **Applikationsbasierte Bedrohungen**
    
    -   Gefahren durch Apps aus Drittanbieter-Stores
        
    -   _Zusätzliche Information:_ Phishing-Angriffe über gefälschte App-Updates.
        

### 7.2 Bedrohungstypen

1.  **Malware**
    
    -   Ziel: Datendiebstahl, Fernsteuerung, Geräteschädigung
        
    -   Verbreitung über Schwachstellen oder infizierte Apps
        
2.  **Spyware**
    
    -   Ziel: Informationsdiebstahl
        
    -   Verbreitung über Social Engineering oder Schwachstellen
        
3.  **Grayware (PUA/Adware)**
    
    -   Ziel: Datensammlung für Werbung oder Marketing
        
    -   Oft mit legitimen Funktionen kombiniert
        

### 7.3 Entwicklung von Bedrohungen

-   **Ziele von Angreifern**
    
    -   Sensible Daten (Kontakte, Online-Konten)
        
    -   Nutzung infizierter Geräte als Botnetz
        
-   **Neue Bedrohungen**
    
    -   **Ransomware/Cryptolocker**:
        
        -   Sperren oder Verschlüsseln von Daten gegen Lösegeldforderung
            
        -   _Zusätzliche Information:_ iOS ist weitgehend resistent gegen Ransomware, aber Nutzer können durch Social Engineering getäuscht werden.