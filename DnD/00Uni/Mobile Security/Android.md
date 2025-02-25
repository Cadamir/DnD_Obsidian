# Android

## 1. Allgemein
- Entwickelt von Google auf Linux-Basis
- Open-Source
- Marktplatz: **Google Play**
- Manuelle Installation von Apps möglich (.apk-Dateien)
- **Starke Fragmentierung**: Unterschiedliche Hersteller passen Android an, was Updates verzögert
- **Custom ROMs möglich**: Nutzer können alternative Android-Versionen installieren (z. B. LineageOS)
- **Google Mobile Services (GMS)**: Enthält proprietäre Google-Dienste (z. B. Google Play, Maps, Assistant)
- **Alternative App-Stores**: Neben Google Play existieren Amazon Appstore, F-Droid, Huawei AppGallery etc.
- **Android-Versionen und Updates**:
    - Jährliche neue Major-Versionen
    - **Project Treble** erleichtert Updates für Hersteller
    - **Android One & Android Go** bieten länger garantierte Updates für bestimmte Geräte
- **Android-Distributionen für spezifische Einsatzzwecke**:
    - **Android Automotive** für Autos
    - **Wear OS** für Smartwatches
    - **Android TV** für Smart-TVs

## 2. Architektur 
###  2.1 Vier Schichten des Systems
1.  **Linux-Kernel**
    - Hardware-Abstraktionsschicht
    - angepasst an die entsprechende Hardware
    - Hauptaufgabe:
		- Verwaltung von Prozessen 
			- PID, Status, Threads, zugehörige Dateien
		- Verwaltung von Speicher
		- Enthält Sicherheitsmechanismen
		- Netzwerkverwaltung
2.  **Systembibliotheken & Laufzeitumgebung**
    - Systemnahe Bibliotheken (C/C++) für Apps und System
    - Laufzeitumgebung
		- optimiert für geringen Stromverbrauch
		- früher: Dalvik Virtual Machine (DVM)
		- jetzt: Android Runtime (ART)
			- Just-in-Time & Ahead-of-Time
3.  **Applikationsframework**
    - Ermöglicht Zugriff auf Hardware und Systemressourcen
    - wichtigste APIs:
		- **Activity Manager** (Steuerung des App-Lebenszyklus)
		- **Content Provider** (Datenverwaltung und -austausch)
		- **Resource Manager** (Verwaltung von UI-Ressourcen, z. B. Bilder, Layouts)
		- **Location Manager** (GPS- und Standortdienste)
		- **Notification Manager** (Benachrichtigungen steuern)
4.  **Applikationen**
    - Entwickelt in Java, Kotlin oder C/C++ (native Bibliotheken)
    - Besteht aus verschiedenen Komponenten (Activities, Services, Broadcast Receivers, Content Providern)
    - Kommunikation über Intents
    - Berechtigungskozept

### 2.2 Android App-Aufbau
- Komponenten
	- **Activities**: Steuern die UI und das Verhalten der App
	- **Services**: Hintergrundprozesse ohne UI
	- **Broadcast Receivers**: Reagieren auf System- oder App-Ereignisse
	- **Content Provider**: Ermöglichen Datenaustausch zwischen Apps
	- **Shared Preferences**: Speicherung kleiner Datenmengen (kein sensibler Inhalt)
	- **Intents**: Kommunikation zwischen Komponenten (explizit oder implizit)
- **Quellcode → Bytecode → DEX-Datei → APK-Paket**
- Android-Manifest (`AndroidManifest.xml`): Definiert Berechtigungen, Intents, Listener etc.
- Wichtige APK-Bestandteile:
    - `META-INF`: Signaturinformationen
    - `lib`: Architekturabhängige native Bibliotheken
    - `res`: Ressourcen (nicht vorkompiliert)
    - `classes.dex`: DEX-Bytecode (aus Java oder Kotlin)
 
## 3. Sicherheitsmechanismen in Android
### 3.1. Sandbox & Isolation
- Jede App läuft in einer eigenen virtuellen Maschine
- Jede App hat eine eigene UNIX-User-ID (UID)
- **Discretionary Access Control (DAC)**: Zugriffskontrolle zwischen Apps mit unterschiedlichen UIDs
- **Mandatory Access Control (MAC) mit SELinux**: Strikte Zugriffsbeschränkung für Systemressourcen

### 3.2. Verified Boot & Dateiverschlüsselung
- **Verified Boot**: Prüft Integrität der Firmware und Systempartition
- **File-Based Encryption (FBE)**: Dateien werden individuell verschlüsselt
- **Scoped Storage**: Apps haben nur Zugriff auf eigene Dateien

### 3.3. Berechtigungskonzept
- **Normale Berechtigungen**: Automatische Gewährung (z. B. Internet)
- **Gefährliche Berechtigungen**: Nutzer muss zustimmen (z. B. Kamera)
- **Signature-Berechtigungen**: Erlaubt nur für Apps vom selben Entwickler
- **Spezialberechtigungen**: Tiefgreifende Befugnisse (z. B. System-UI-Anpassungen)
- Berechtigungsanfragen über `checkSelfPermission()` und `requestPermission()`
- **Automatischer Berechtigungsentzug** bei längerer Inaktivität

### 3.4. Trusted Execution Environment (TEE)
- Geschützte Umgebung für sicherheitskritische Operationen (z. B. Schlüsselspeicherung)
- Hardwareunterstützung durch TPM (Trusted Platform Module)
- Ein **isolierter Bereich** innerhalb des Prozessors, der **sichere Berechnungen** unabhängig vom Hauptbetriebssystem (REE = Rich Execution Environment) durchführt.
- Geschützt gegen **Malware, Root-Zugriffe und normale Apps**.
- Wird meist für **Kryptografie, Biometrie, DRM (Digital Rights Management) und sicheres Booten** genutzt.
-   **getrennte Speicher- und Rechenkapazitäten:**
    -   Das TEE hat seinen eigenen Speicher und eigene CPU-Ressourcen.
    -   Apps im normalen Betriebssystem (REE) **können nicht direkt auf das TEE zugreifen**.
-   **Zwei Modi im Prozessor:**
    -   **"Normal World" (REE, Rich Execution Environment)** → Hier laufen Android, Apps, Spiele etc.
    -   **"Secure World" (TEE, Trusted Execution Environment)** → Hier laufen sicherheitskritische Operationen.
-   Wenn eine App z. B. einen **sicheren Fingerabdruck-Scan** macht, wird die Anfrage an das TEE weitergeleitet, dort verarbeitet und das Ergebnis an die App zurückgegeben.
-   **Schlüsselverwaltung & Kryptografie:**
    -   Speicherung und Verarbeitung von Verschlüsselungsschlüsseln.
    -   Wird für **Android Keystore** genutzt, um **private Schlüssel sicher zu speichern**.
-   **Biometrische Authentifizierung:**
    -   Fingerabdruck- und Gesichtserkennung laufen im TEE, damit die Daten nicht von normalen Apps ausgelesen werden können.
-   **Sicherer Bootprozess:**
    -   **Verified Boot** prüft beim Starten des Geräts, ob das Betriebssystem manipuliert wurde.
-   **DRM (Digital Rights Management):**
    -   Apps wie Netflix oder Spotify nutzen das TEE, um **geschützte Medieninhalte sicher wiederzugeben**.
 -   Selbst wenn das Android-System mit Malware infiziert ist, bleibt das TEE **unangreifbar**.
-   TEE ist in **Hardware implementiert**, daher schwer zu hacken.
-   Root-Zugriff oder Custom-ROMs **haben keinen Zugriff** auf das TEE.
-   Apps können auf TEE-Dienste nur über **System-APIs oder spezielle Schnittstellen** zugreifen.
-   -   **heoretisch angreifbar:**
    -   Sicherheitslücken im TEE könnten durch **Side-Channel-Angriffe** ausgenutzt werden.
    -   In der Vergangenheit gab es Angriffe auf Qualcomm’s **QSEE** und ARM’s **TrustZone**.
-   **Proprietär & schwer zu prüfen:**
    -   Der Code des TEE ist oft **nicht Open Source** → schwer für unabhängige Prüfungen.
-   **Performance-Einschränkungen:**
    -   Das TEE ist leistungsbeschränkt → nur für sehr spezifische Berechnungen geeignet.

## 4 Berechtigungskonzept
### 4.1 Arten von Berechtigungen
- Normal
	- wird einfach gegeben
- Dangerous
	- wird nachgefragt
- Signatur
	- automatisch gewährt für Apps vom gleichen Entwickler
- Special
	- tiefer Eingriff
	- besondere Vergabe-Methode
- Custom
	- eigene Berechtigung um auf Funktionen einer App zuzugreifen

### 4.2 Berechtigungsanfrage
-   **Zur Laufzeit anfordern** -> **Dangerous Permissions** 
-   **Berechtigungsgruppen**:
    -   oft in Gruppen
    -   Wenn Nutzer eine Berechtigung innerhalb einer Gruppe gewährt -> anderen Berechtigungen derselben Gruppe automatisch gewährt.
-   **Berechtigungsrationale**:    
    -   **Erklärung** um den Nutzer zu überzeugen
		-   `shouldShowRequestPermissionRationale()`

#### 4.3. Berechtigungsanfragen UI
- Dialoge und Benachrichtigungen
- Einstellungsseiten
- Zugriffs-Icons

#### 4.4. Sensible Daten und Datenschutz
-   **Scoped Storage**:
    - ab **Android 10**
    - Zugriff auf Dateien zu kontrollieren
    - Apps können nur auf ihre eigenen Dateien zugreifen
-   **Fremdzugriff auf Daten durch Content Provider**

#### 4.5. Benutzerverwaltung**
-   **Zugriffsrechte basierend auf Benutzerprofilen**

#### 4.6. Sicherheitslücken und mögliche Angriffe**
-   **Exfiltration von Daten**:
    -   Schadhafte Apps könnten mehr Berechtigungen anfordern, als sie tatsächlich benötigen, und so sensible Daten wie Kontakte oder Nachrichten stehlen.
-   **Berechtigungseskalation**:
    -   Eine bösartige App könnte versuchen, **Berechtigungen zu eskalieren** oder Sicherheitslücken auszunutzen, um mehr Rechte zu erlangen und auf unerlaubte Daten oder Funktionen zuzugreifen.

## 5. Sichere Kommunikation zwischen Apps

### 5.1. Intents

-   Standard-Kommunikationsmechanismus
-   Intent-Filter im Manifest zur Einschränkung des Zugriffs
-   **Explizite Intents**: Bestimmte App als Ziel
-   **Implizite Intents**: System entscheidet, welche App reagiert

### **5.2. IPC & Binder-Mechanismus**

-   **Inter-Process Communication (IPC)**: Ermöglicht Datenaustausch zwischen Apps
-   **Binder**: Middleware für sicheres RPC (Remote Procedure Call)
-   Zugriffskontrolle durch **Service Manager** und Berechtigungen


Klar! Hier ist eine detailliertere Ausführung zum Thema „Sichere Kommunikation zwischen Apps“ auf Basis der Android-Sicherheitsarchitektur, unter Verwendung klarer, prägnanter Stichpunkte:

## 5. Sichere Kommunikation zwischen Apps
### 5.1. Grundlegende Prinzipien
-   über **Interprozesskommunikation (IPC)**.
-   **IPC-Mechanismen** Zugriff nur durch autorisierte Apps auf die Daten anderer Apps zugreifen
### 5.2.  Wichtige Mechanismen
-   **Intents**:
	-   **Definition**: Ein Intention (Intent) ist ein Nachrichtentyp, der eine Aktion beschreibt, die von einer App ausgeführt werden soll.
    -   **Arten**:
		-   **Explizite Intents**: Kommunikation zwischen Komponenten derselben App oder mit genau festgelegten Komponenten einer anderen App.
        -   **Implizite Intents**: Eine Anfrage an den System- oder App-Manager, die eine passende App zur Ausführung der gewünschten Aktion auswählt.
        -   **Sicherheitsaspekte**:
            -   Intents, die sensible Daten enthalten (z. B. private Informationen), sollten **verschlüsselt** oder über sichere Kanäle übertragen werden.
            -   **Berechtigungen** können festgelegt werden, um den Zugriff auf Intents zu beschränken (z. B. über Manifestberechtigungen).
-   **Content Providers**:        
    -   **Definition**: Ein Content Provider ermöglicht Apps, Daten untereinander auszutauschen, indem er eine standardisierte API für den Zugriff auf persistente Daten bereitstellt (z. B. Kontakte, Kalender).
    -   **Sicherheitsaspekte**:
        -   **Berechtigungen** definieren, um festzulegen, welche Apps auf ihre Daten zugreifen können.
        -   **Sichtbarkeit und Zugriff**: Apps sollten nur auf Content Providers zugreifen, die explizit mit den entsprechenden Berechtigungen freigegeben sind.
        -   **Datenverschlüsselung**: Sensible Daten sollten verschlüsselt sein, wenn sie über einen Content Provider ausgetauscht werden.
        -   **Sharing Permissions**: Content Provider unterstützen oft **Shared User IDs** oder **Signature Permissions**, um den Zugriff nur für Apps zu gewähren, die von demselben Entwickler signiert wurden.
-   **BroadcastReceiver**:        
    -   **Definition**: Ein BroadcastReceiver empfängt und reagiert auf systemweite oder app-spezifische Broadcasts (z. B. Systemereignisse wie Netzwerkinformationen oder App-spezifische Ereignisse).
    -   **Sicherheitsaspekte**:
        -   **Sicherheitsfilter**: nur berechtigte Apps -> **Intent-Filter** & **Sicherheitsrichtlinien**
        -   **Privates Broadcasting**: nur an bestimmte App
-   **AIDL (Android Interface Definition Language)**:        
    -   **Definition**: Ein Mechanismus, um **komplexe Datenstrukturen** zwischen Apps oder verschiedenen Prozessen auszutauschen. AIDL ermöglicht es, dass Apps Funktionen aufrufen, die in einer anderen App oder einem anderen Prozess laufen.
    -   **Sicherheitsaspekte**:
        -   Zugriff auf entfernte Dienste nur für autorisierte Apps
        -   Berechtigungen und **Interprozesskommunikation (IPC)**-Sicherheitsmechanismen streng überprüft
        -   **Service-Schutz**: Der Service sollte Anfragen genau prüfen, um Missbrauch zu verhindern

### 5.3. **Zugriffssteuerung und Berechtigungen**    
-   **Manifestberechtigungen**:
    -   Alle Mechanismen zur Kommunikation (z. B. Intents, Content Providers) können durch Manifestberechtigungen geschützt werden.
    -   Diese Berechtigungen müssen beim Starten der App explizit angefordert und vom Nutzer genehmigt werden
-   **Berechtigungsprüfung zur Laufzeit**:
    -   Für gefährliche Berechtigungen müssen Apps beim Zugriff während der Laufzeit nach der Genehmigung des Nutzers fragen.
    -   **Prüfung von Berechtigungen** mit `checkSelfPermission()`

### 5.4.  **Sicherheitsvorkehrungen bei IPC**    
-   **Binder IPC**:
    -   Kommunikation mit Systemdiensten oder anderen Apps in einem eigenen geschützten Speicherbereich
    -   **Zugriffssteuerung**: **Binder** prüft, ob der aufrufende Prozess die richtigen Berechtigungen hat, um auf den Zielprozess zuzugreifen.
    -   **Zugriffsbarrieren**: Bei der Kommunikation über Binder müssen explizite **Berechtigungsprüfungen** durch das Betriebssystem erfolgen.
-   **Signierte Kommunikation**:
    -  durch **Digitale Signaturen** (basierend auf Zertifikaten) sicherstellen, dass die Kommunikation nur zwischen vertrauenswürdigen Apps erfolgt.
    -   **Signature-Based Permissions**: dieselbe digitale Signatur -> Zugriff auf Ressourcen oder Funktionen, die anderen Apps nicht zugänglich sind

### 5.5.  **Verschlüsselung und sichere Kanäle**    
-   **Verschlüsselung**:
    -   **Ende-zu-Ende-Verschlüsselung** sollte implementiert werden, wenn sensible Daten zwischen Apps übertragen werden.
    -   Auch bei der Kommunikation über ungesicherte Kanäle (z. B. HTTP) sollten Mechanismen wie **SSL/TLS** verwendet werden, um Daten während der Übertragung zu schützen.
-   **Zertifikats- und Schlüsselverwaltung**:
   -   **Schlüsselverwaltungssysteme** wie das **Android Keystore System** sorgen dafür, dass private Schlüssel für kryptografische Operationen sicher gespeichert werden.
   -   Apps sollten **verschlüsselte Daten** verwenden, wenn sensible Daten über IPC-Mechanismen wie AIDL oder Content Providers übertragen werden.
### 5.6.  **Best Practices für sichere Kommunikation**    
-   **Vermeidung von ungeschütztem Broadcast**:
    -   Sende und empfange nur Broadcasts, die entweder an **bestimmte Empfänger** gerichtet sind oder durch die Verwendung von **sicheren Berechtigungen** geschützt sind.
-   **Verwendung von sicheren API-Mechanismen**:
    -   Nutze standardisierte APIs wie **Content Providers** und **Intents** mit gezieltem Zugriffsschutz, um Interaktionen zwischen Apps sicher zu gestalten.
-   **Vermeidung von unsicheren Intents**:
    -   Verwende bei der Kommunikation über **implizite Intents** **Intent-Filter**, um sicherzustellen, dass nur die vorgesehenen Empfänger die Intents erhalten.