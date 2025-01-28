# Mobile Security

## Was ist ein Smartphone
- neue Software kann installiert werden
- Internetzugriff
- private und vertrauliche Daten 

## Allgemeine Bedrohungen

|Gefahr|Schutz|
|-|-|
|Verlust|PIN|
|Daten in der Cloud|Verschlüsselter Download, sichere Authentifizierung|
|Spionierende Anwendungen|Mit den eigenen Daten sinnvoll umgehen, nur Rechte erlauben, die notwendig sind
|Manipulierte Anwendungen|Vertrauenswürdige Quellen
|Social Engineering|Niemanden im Internet vertrauen
|Drahtlose Datenübertragung|WPA3, Verschlüsselung, nur bekannte WLANs nutzen
|Finanzielle Schäden|In-App-Käufe deaktivieren, Online Banking sicher betreiben

## Beteiligte Parteien
|Parteil|Hauptinteresse
|-|-
Benutzer|Daten sicher, Gerät funktioniert
Hardware-Hersteller|Rechtliche Richtlinien werden eingehalten, International Mobile Equipment Identifier (IMEI) darf nicht veränderlich sein
Netzbetreiber|Benutzer soll das eigene Netz nutzen und eigene Services und keine Drittanbieter
Software-Entwickler/Dienstanbieter|Alle Apps installierbar, Apps nicht kopierbar
Betriebssystem-Anbieter|Gute Basis, damit das Handy weiter genutzt wird; bietet Grundlage für Apps
Marktplatzbetreiber|viele Apps und Käufe durch den Nutzer, Schutz des Nutzers durch Qualitätskontrollen an den Apps
Administratoren|Sicherheit der Daten (besonders Firmendaten), unvorsichtige Benutzer ist die größte Gefahr

## Riskantes Nutzerverhalten
- kein Gefahrenbewusstsein
- geringes Wissen über Schutzmaßnahmen
- Berechtigungsanfragen durch Apps einfach akzeptiert
- Apps aus unbekannten Quellen einfach installiert (trotz Warnung)
- keine Datenverschlüsselung durch Nutzer
- Geräteeigene Dienste werden gedankenlos verwendet
- 
## Allgemeine Sicherheitsmechanismane
### Schichtenarchitektur
- Basis: Hardware
    - Programmausführung
    - bereitsstellung einiger Sicherheitsfunktionen
- Betriebssystem
    - Kern: Steuerung Hardware
    - Schnittstellen für Anwendungen
        - System-Dienste
        - System-Bibliotheken
- Anwendungen
    - mit oder ohne Userinterface

### Platform Security Architecture
- Software-Komponenten die zwischen Hardware und Anwendungen liegt
- vom Hersteller mitgeliefert
- Grundprinzipien:
    - Isolierung der Programme (Sandbox, Kommunikation nur über IPC [Interprozesskommunikation])
    - Zugriffskontrollmodell (Enge Zusammenarbeit von IPC mit Refrence Monitor, nur bei korrekter Berechtigung darf IPC verwendet werden)
    - digital signierte Programme (Programme eindeutig erkennbar und unverändert)

### Software-Verteilung
- ist der Hersteller vertrauenswürdig
- ist die Software sicher
- Kontrolle der Zugriffsrechte für die Software (Reference Monitor)
    - App fragt Zugriffsrecht beim Nutzer an
    - Nutzer kann entscheiden, ob die App das braucht
    - kann normale Nutzer überfordern, deshalb Vereinfachung der Zugriffsrechte für leichte Nutzung
    - Bei Abstraktion: Nutzer muss es verstehen und nur korrekte Rechte hinter Abstraktion verstecken
    - Änderung nur durch Nutzer, nicht durch Software
- Laufzeitumgebungen
    - Virtuelle Maschienen für die einzelnen Anwendungen
    - eigener Adressraum (und auch nur Zugriff darauf)
    - Daten auf Hardware verschlüsselt (zentral gesteuert durch Systemaufrufe)
    - VPN für Netzwerkkommunikation

### Mangement
- Trennung dienstlicher und privater Daten
- Nutzung von EMM (Enterprise Mobility Management) für schnelle Betreuung vieler Geräte (zB. Intallationsrechte, Löschungsrechte, ...)

## Betriebssysteme
### Android
- von Google auf Linuxbasis
- Open Source
- GooglePlay = zentraler Verteiler
- freie Installation möglich (mit AndroidPackage .apk)
- Software beschrieben durch Manifestdatei AndroidManifest.xml
    - enthaltene Komponenten
    - Hardwareeigeneschaften für Ausführung
    - benötigte Berechtigungen
- Ausführung in eigener JVM
- eigene UNIX user id für jede Anwendung
    - Zugriffsrechtekontrolle läuft darüber
    - Erst Rechtekontrolle, dann echter Funktionsaufruf
- Laufzeitumgebung: Andreoid Runtime (ART)
- Abschottung der Anwendudngen
    - durch Linux-Kernel implementiert
    - jede Anwendung: eigene VM, eigene UID, eigener Prozess
    - Discretionary Access Controlls (DAC) verhindern Interaktion zwischen Anwendungen mit untschiedlichen UID
    - gleicher Entwicklungsschlüssel = gleiche UID
- SELinux: Untersützung von Systemrichtlinien
    - Kontrolle von Subjekten (Prozesse) auf Objekte (Ressourcen) durch Mandatory Access Controlls (MAC)
- Verified Boot
    - kontrolliert Unveränderlichkeit der Gerätesoftware
    - device-mapper-verizy verifiziert den Bootprozess
    - berechnete Hashwerte werden mit gespeicherten Hardware-Hash verglichen (SHA 256)
- Alles wird verschlüsselt im Flash-Speicher
- Der Master-Key (Disk Encryption Key DEK) ist auch verschlüsselt durch einen Kombination aus PIN und einem Salz
- jedes Profil hat einen eigenen Schlüssel
- auch Datei-Verschlüsselung
- Scoped Storage
    - nur Zugriff auf eigene App-Dateien
    - Datenbank ist die Zuordnung gespeichert
    - keine Verschmutzung öffentlicher Ordner

### iOS
- von Apple auf Basis von OS X
- keine freie Software
- Installation von Apps **nur** AppStore
    - Entwickler muss sich registrieren
    - Programm digital signieren
    - Überprüfung vor Einstellen in AppStore durch Apple
- Hardware mit Sichreehtiseigenschaften
- Boot-ROM hat auch Zertifikat von Apple zum Prüfen von digitalen Signaturen
- Codeausführung nur mit gültiger Signatur
- AES (Advanced Encryption Standard) als Hardwareimplementierung
- eindeutige Geräte-ID in Hardware
    - Verschlüsselungsschlüssel vom RAM 
- jede Datei bekommt eigenen Schlüssel, der mit Hauptschlüssel verschlüsselt ist
- Jede Datei hat eigenes Home-Verzeichnis
- Andere Dateien können andere Verzeichnisse zwar erraten, aber alles nur verschlüsselt lesen
- löschen von Dateien: einfach zugehörigen Schlüssel löschen
- Dateien zwischen Programmen teilen: einfach Schlüssel teilen
- Dateischlüssel = symetrische Schlüssel

## weitere Maßnahmen
1. Prävention
2. Überwachung
3. Schadensminderung

### Prävention
1. Codebeurteilung
2. Marktplatzbezogene Untersuchung von Apps
3. Compartmentalisation (Trennung dienstlich & privat)
4. Security-Policies (Welche Geräte kommen worauf)
5. Trusted Party Management (Verifikation von Zertifikaten, Schlüssel und Signaturen)
6. Authentisierungsmechanismen
7. zeitlich begrenzter Zugang/Zugriff

### Monitoring
1. Verhaltensanalyse
2. Log-Analysen
3. Hooking von Systemaufrufen (feingranulare Logs von Systemaufrufen)
4. Berechtigungsüberprüfung zur Laufzeit

### Schadensminderung
1. Aktualisierung von Systemen und Apps
2. Long-Term Support
3. Fernzugriff (durch zB. Adiminstratoren oder zum sperren)
4. Ortserfassung (Wiederfinden)
5. Bekanntgabe von Verwundbarkeiten
6. Rückruf zur Analyse (Gerät wird zur Analyse zurückgeschickt)
7. Nutzer-Feedback (abnormales/verdächtiges Verhalten melden)
8. Dokumentation

## Sicherheitsbezogene Einstellungen
1. Aktualisierung von Betriebsystemen un dApps
2. Vermeidung von Rooting und Jailbreaking (bei der Entwicklung von Apps)
3. Verschlüsselung von Geräten und Dateien (starke Passwörter, ...)
4. Verlorene Geräte wiederfinden (mithilfe von Apps)
5. Bildschirmsperre und Sperrbildschirm
6. zwei-Faktor-Authetifizierung
7. Sicherheitssoftware
8. Apps und ihre Berechtigungen
9. Absicherung von Netzwerkverbindungen (VPN)
10. Verbesserung des Datenschutzes (Online-Konten-Einstellungen, App-Aktivitätsnachforschungen, ...)

# Benutzersicherheit im Internet
## Architektur der mobilen Betriebssysteme Android und iOS 
### Android
- 4 Schichten
    - Linux-Kernel
    - Systembibliotheken und Android Laufzeitumgebung
    - Applikationsframework
    - Applikation
- Jede Schicht hat konkrete Interfaces und Systemressourcen für darüberliegende Schicht
- Linux-Kernel
    - angepasst auf die Hardware
    - Verwaltung laufender Prozesse (Infos über Prozess im task_struct)
        - **Identifier (PID)**
        - Prozessstatus
        - Thread
        - zugehörige Dateien
    - Verwaltung von Speicher
    - einige Sicherheitsmechanismen
- Systembibliotheken und Laufzeitumgebung
    - Bibliotheken in C/C++
        - genutzt durch System und Apps
    - Laufzeitumgebung: Dalvik Virtaul Machine (DVM) + Java-Bibliotheken
        - geringer Stromverbrauch
        - geringer Rechenleistungsbedarf
- Applikationframework
    - stellt die APIs bereit
    - ermöglicht Entwicklern Zugriff auf Features des Endgerätes ohne tiefe Kenntnisse des individuellen Gerätes
- Applikation

#### App-Aufbau unter Android
- meist in Java
- auch native Bibliotheken in C/C++ nutzen.
- Build: Java-Code in Bytecode für DVM umgewandelt (DEX-Datei) 
- Android-Manifest -> Definition von Berechtigungen, Intents, Listener, Receiver
- mit Bytecode, Bibliotheken und weiteren Ressourcen wird die App in ein APK-Paket verpackt
- APK-Paket ist installierbar
    - `META-INF` -> Verzeichnis
        - `MANIFEST.MF` -> Manifest in kompilierter Form
        - `CERT.RSA` -> Zerfifikat, mit der die Applikation signiert wurde
        - `CERT.SF` -> Liste aller Ressourcen und das SHA-1-Digset
    - `lib` -> Verzeichnis
        - `armeabi` -> kompilierter ARM-Code
        - `armeabi-v7a` -> kompilierter ARMv7-Code
        - `x86` -> kompilierter x86-Code
        - `mips` -> kompilierter MIPS-Code
    - `resources.arsc` -> vorkompilierte Bibliotheken
    - `res` -> Verzeichnis für Bibliotheken, die nicht in resources.arsc kompiliert wur5den
    - `AndroidManifest.xml` -> ein weiteres Manifest mit wichtigen Metainformationen (als XML)
    - `classes.dex` -> kompilierter Java-Code
- Bestandteile:
    - Android Manifest
        - Allgemeines: Name, SDK-Version, eigene Version, angeforderte Berechtigungen, Hard- und Softwareanforderungen
        - alle Activities enthalten
            - als MAIN-Activity ist der App-Start hinterlegt (Was passiert wenn ich aufs App-Icon drücke)
            - Externe Events definiert, die eine Activity starten 
    - externe Bibliotheken
    - Acitivites
        - Bereitstellung einer interaktiven GUI
        - passiver Modus, wenn aktivity im Hintergrund (nicht persistente Daten werden dann gelöscht)
    - Services
        - im Hintergrund ohne Nutzerinteratkionen
        - im Manifest deklariert
        - Service-Manager mitgeteilt
        - Bei Definition Festlegung, wie andere Komponenten der App mit ihm kommunizieren
        - können z. B. Dateisystemoperationen durchführen, Netzwerkverkehr überwachen oder Standortdaten an andere Apps weitergeben.
        - Zugriffsregelung auf Service durch "Binder IPC" und "Service Manager" 
        - Service Manager prüft Zugriffsrechte
        - Binder IPC = Kommunikationsschnittstelle zwischen App und Service.
    - Broadcasts Receiver
        - ist eine Activity
        - kann durch implizite Intents aktiviert werden
    - Shared Preferences
        - speicherung kleinerer Datenmengen
        - meist als XML im Verzeichnis `shared_prefs`
        - niemals hier sensible Daten
    - Intents
        - spezieller Datentyp
        - Kommunikation zwischen Komponenten
        - Pflichtattribute: Data & Action
        - Freiwillige Attribute: Types, Categories, Extra
        - Aktion: was macht die App mit den Daten
        - Typ: Nur wenn Daten nicht als URI mitgeschickt werden, MIME-Typ
        - Category: genauere Aktion
        - implizit: an Paketmanager, der eine passende App sucht (passender BroadcastReceiver)
        - explizit: ganz konkreter Addressat, meist nur innerhalb einer App
    - Content Provider
        - Datentyp
        - um persistente Daten für andere applikationen zugägnlich zu machen
        - für Datenaustausch zwischen Apps
        - Apps nutzen SQL/relationale DB-Schnittstellen um Daten zu verarbeiten
### IOS
- Core OS (Darwin)
    - UNIX-Betriebssystem
    - ohne GUI
    - Schnittstelle zur Hardware, Speicher, Dateisystem und zur untersten Schicht des Netzwerkstacks
- Core & Security Services
    - Systembibliotheken
    - API für ICloud
    - SQLite-DB Schnittstellen
    - Internetkommunikation
    - Sicherheit
        - Dataprotection
        - Automatic Reference Counting
    - InAppKäufe
    - Adressbuchzugriff
    - Keychain
    - Aufrufe beginnen immer mit `CF` (zB. `CFReadStream`)
- Media
    - Bibliotheken für alles auf dem Bildschirm
    - zB. Grafiken, Videos, Audio, AirPlay
- Cocoa Touch
    - für Entwickler einfache Schnittstellen für darunterliegende Bibliotheken
    - *Foundation*: relevante Basisklassen (zB. String, Array) 
    - *UIKit*: 
        - *AppKit* : für UI (zB. Buttons, Menüs)
        - *CoreData*: für Objektgrafen und Ablage von Daten
- Applications
- immer Schnittstellen und Ressourcen für darüberliegende Schicht
#### App-Aufbau unter IOS
- in Objective-C oder Swift
- als IPA-Datei installiert (Datei-Paket)
    - `Payload/Applikation.app` -> alle statischen Ressourcen/Bibliotheken/kompilierte Applikation
    - `iTunesArtwork` Icon für ApStore und iTunes
    - `iTunesMetadata.plist` Metadaten wie zB. Entwicklername oder Versionsnummer
- das Executable
    - als Mach-O-Dateityp
    - solange verschlüsselt, bis sie installiert wird (dann komplett entschlüsselt)
    - mehrere Ausführbare Dateien für jede mögliche Architektur (*FatBinaries*)
    - **Header** -> Beginn, wichtige Metadaten
    - **Load-Kommandos** -> Beschreibung des Layouts
        - initiales Layout der Datei im Speicher
        - Speicherort der  Symboltabelle
        - Informationen zu verschlüsselten Bereichen
        - verlinkte Shared Libaries
    - **Data** -> Segmente und ihre Code- und Datenbereiche

### Dateischutz
#### Dataprotection Level
- `NSFileProtection` -> Systemeigene Verschlüsselung von Daten
- Schlüsselgeneriung durch: 
    - Hardware -> einzigartiger Schlüssel aus der Hardware
    - Passcode -> Passcode zum Telefonentsperren
- **NSFileProtectionNone** 
    - Verschlüsselung bei ausgeschalteten Gerät
    - Schlüssel aus Variablen auf Gerät
    - kaum Schutz
- **NSFileProtectionCompleteUntilFirstUserAuthentication**
    - Standard für Drittanbieter
    - Schlüssel aus Variablen und Passcode
    - Schlüssel bleibt bis zum Ausschalten vorhanden
- **NSFileProtectionCompleteUnlessOpen**
    - Schlüssel aus Variablen und Passcode
    - Schlüssel immer gelöscht, sobald die entsprechende Datei geschlossen wird
    - Interaktion bei gesperrten Bildschirm möglich
    - Schlüssel lebt nur solange, wie notwendig
- **NSFileProtectionComplete**
    - Sobald Bildschrim gesperrt -> Schlüssel gelöscht
    - Schlüssel aus Variablen und Passcode

#### Keychain
- wie ein Safe
- für hoch sensible Daten
- `kSecAttr` -> API mit vorgefertigten Schutzlevel
- **kSecAttrAccessiblerAlways** -> immer verfügbar und zugreifbar
- **kSecAttrAccessibleAfterFirstUnlock** -> Verfügbar nach ersten Entsperren nach Neustart bis zum Ausschalten
- **kSecAttrAccessibleWhenUnlocked** -> Verfügbar, wenn Gerät entsperrt ist
- **kSecAttrAccessibleAlwayThisDeviceOnly** -> Verfügbar von nur einem Gerät (auch nicht bei Backup)
- **kSecAttrAccessibleAfterFirstUnlockThisDeviceOnly**
- **kSecSttrAccessibleWhenUnlockedThisDeviceOnly**
- **kSecAttrAccessibleWhenPasscodeSetThisDeviceOnly**
    - Daten können nicht auf anderes Handy übertragen werden
    - nur solange ein Passcode auf dem Handy existiert
        - sollte der Passcode später entfernt werden, hat man keinen Zugriff mehr auf die Daten

### Bootschutz - Secure Boot Chain
- Start des Betriebssystems und der Firmware
    - Boot ROM -> LLB -> iBoot -> iOS Kernel
- relevante Komponenten sind digital signiert
    - diese Signatur wird jedesmal überprüft
    - Änderungen können festgestellt werden
- Boot ROM
    - unveränderlich (Hardware auf Chip)
    - enthält den Public-Key für die Apple Root-CA
    - Schlüssel für die Verifizierung des Low Level Bootloader
- Low Level Bootloader (LLB)
    - viele Routinen zum Starten von weiteren Programmen
    - iBoot im Flash-Speicher finden und Signatur verifizieren
        - Bei Fehlschlag: Handy startet im *Recovery Modus*
- iBoot
    - überprüft Signatur des iOS-Kernels
- iOS Kernel
    - lädt das eigentliche Betriebssystem
- schweres umgehen
- Wenn umgangen: Jailbreak

## Bedrohungszenarien
- **Kategorien**:
  - Webbasierte Bedrohungen
    - Nutzung von Schwachstellen in mobilen Browsern.
    - Funktionen wie Video-Player oder Videoanrufe können ausgenutzt werden.
    - Schadlinks via E-Mail oder Social Media.
  - Applikationsbasierte Bedrohungen
        - Über Drittanbieter-Märkte
        - Geringe Sicherheitsprüfungen bei Apps.
- **Typen**:
  1. **Malware**:
     - Ziele: Datendiebstahl, Fernsteuerung, Beschädigung.
     - Verbreitung: Täuschende Apps oder Schwachstellen.
  2. **Spyware**:
     - Ziel: Informationen sammeln und weiterleiten.
     - Verbreitung: Physischen Zugriff, Schwachstellen oder Social Engineering.
  3. **Grayware (PUA/Adware)**:
     - Ziel: Datensammlung für Werbung, Marketing.
     - Teilweise echte Funktionen integriert.
     - PUA = Potentially Unwanted Application (mehr Funktionen als gewollt, auch negative)
- **Entwicklung von Bedrohungen**:
    - **Beliebte Ziele**:
        - Sensible Daten (Telefonbuch, Online-Konten).
        - Botnetz-Funktionalitäten (C&C-Server).
    - **Neue Bedrohungen**:
        - **Ransomware/Cryptolocker** (2015):
            - Verschlüsselung von Daten, Erpressung.
            - Ransomware sperrt Gerät
            - Cryptolocker verschlüsselt Daten.

### OWASP Top 10
1. Weak Server Side Controls
    - Ist das Backend sicher?
    - Ist die Authentifizierung zum Backend gut?
2. Insecure Data Storage
    - wie und wo sind die Daten abgelegt?
3. Insufficient Transport Layer Protection
    - Sichere Datenübertragung zu WebServices über das Internet 
4. Unintended Data Leakage
    - Nutzung von Systemfunktionen mit ungewollten Nebeneffekten
5. Poor Authorization an d Authentication
    - schwache/unzureichende Nutzerauthentifizerung oder seiner Rechte
    - Daten der App auslesbar
    - nicht alles ist durch Authentifizierung geschützt
    - man kann sich irgendwie als der Nutzer ausgeben
6. Broken Cryptography
    - sichere Verschlüsselungsalgorithmen
    - sichere Passwörter
    - keine Hardcoded Credentials
7. Client Side Injection
    - nicht passender Input erzeugt Fehler -> Ausnutzen dieses Fehlers
8. Security Decisions via untrusted Inputs
    - versteckte Parameter, die sensible Daten beinhalten oder manipuliert werden können
9. Improper Session Handling
    - Vorhersehbare Authentifizierungstoken
    - zu lange Lebensdauer von Sessions
    - gültigkeit auch nach Nutzer Log Out
10. Lack of Binary Protections
    - Schutz vor Reversing

## Labor für die Analyse von mobiler Applikationen einrichten
- Java Installiern
- Android Developer Kit / Android Studio installieren

## Analysemethoden mobiler Applikationen
- statische und dynamsiche Analyse sollte miteinander verbunden werden

### Statische Analyse (Reversing)
- Definition: Untersuchung von Software durch Analyse des Quellcodes ohne Ausführung der Anwendung.
- Vorgehen:
    - Dekompilieren und Zerlegen von Anwendungspaketen.
    - Suche nach Mustern wie Berechtigungen, Funktionsaufrufen oder hart codierten Variablen.
- Vorteile:
    - Schnell und einfach durch automatische Tools (z. B. Muster- oder Signaturerkennung).
    - Keine Ausführung der verdächtigen Anwendung erforderlich.
- Nachteile:
    - Abhängigkeit von bekannten Mustern (schwierig bei neuer Malware oder polymorphem Code).
    - Erschwert durch Obfuscation und verschlüsselten Quellcode.
    - Aufwendiger bei manueller Analyse.
- Erschwerende Faktoren:
    - Verschleierungstechniken (Obfuscation).
    - Verschlüsselte Codebestandteile, die ohne Schlüssel nicht lesbar sind

### Dynamische Analyse
- Definition: Ausführung der Applikation in einer überwachten Umgebung (Sandbox).
- Vorgehen:
    - Sandbox isoliert die Android-Instanz, um schädliche Aktionen zu verhindern.
    - Überwachung und Protokollierung aller relevanten Aktionen.
    - Erstellung eines Berichts mit ausgehenden Verbindungen und Änderungen.
- Vorteile:
    - Umgeht Probleme der statischen Analyse (z. B. Obfuscation, Codeverschlüsselung).
    - Quellcode liegt zur Laufzeit entschlüsselt und im Klartext vor.
- Nachteile:
    - Schadcode erkennt gängige Sandboxen und verhält sich anders auf echten Geräten.
    - Ereignis-Abdeckung: Aktionen werden nur erfasst, wenn spezifische Ereignisse (z. B. SMS-Eingang) ausgelöst werden.


# Berechtigungskonzept von Android
- dient dem Schutz von Nutzerdaten und Zugriff auf Ressourcen
- Berechtiugnskategorien
    - Normal (niedriges Risiko, Gewährung ohne Nutzerinteraktion, zB. Internetzugang)
    - Dangerous (sensible Gerätefunktionen, private Daten, Gewährung nur durch Nutzer, zB: Kamera)
    - Siganture (automatisch gewährt, wenn eine andere App vom gleichen Entwickler das auch so definiert)
    - Special (Tiefgreifende Befugnisse, die alles betreffen könne, gewährt über das Internet als besonderes Nachrichtenobjekt mit Nutzeraktion, zB. System-Einstellungen verändern)
    - Custom Permisson (Aufrufen von Funktionen einer anderen App)
    - Zero Permission (keine konkrete Berechtigungen, sondern die Summe der Zugriffe auf alles ohne Berechtigungen)
- TEE (trusted execution enviorment)
    - nur für TA (trusted applications)
    - für kritische Operationen
    - meist durch besondere Hardware (TPM, trustet platform module) unterstützt
    - zB. Schlüsselerzeugung, Zufallszahlengenerierung, Ver- u. Entschlüsseln
- normale Apps laufen in der REE (rich execuation enviorment)
    - kein Zugriff auf TEE, kein Wissen über TEE
    - Sicherheitsbezogene Aufgaben über Systemaufrufe (was vielleicht an TEE weitergeleitet wird, wenn es eine gibt)
- Berechtigungsgruppen (Zusammenfassung von Berechtigungen für ein Thema)
- Berechtigungsanforderung
    - gefährliche Berechtigung wird angefordert
    - hat die App bereits eine Berechtigung der gleichen Gruppe, wird sie automatisch gewährt
    - hat sie keine: Anfrage-Dialog eingeblendet

## Spezifizierung von Berechtigungen
- Spezifiaktion im Manifest (und nur da)
- nicht über das Programm hinzufügbar
- Attribut andriod:name wird eine Berechtigung aufgeführt
- zu dem `<uses-permission>` Tag sollte auch ein `<uses-feature>` Tag
- Berechtigungen herausfinden einer .apk Datei:
    - android SDK
    - Android Asset Packaging Tool (aapt)
        - `aapt d permissions <AppName>.apk`
    - Android Debug Bridge (adb)
        - `adb shell dumpsys package "packagename" | grep permission`
- eigene Berechtigung definieren: `<permission>`

## Präsentation vn Berechtigungsanfragen
- als Dialog
- als Einstellungsseite
- als Weiterleitung auf die Einstellungsseite
- Berechtigungen zurücksetzten, wenn die App länger nicht genutzt wird
- Teilweise werden Zugriffe auf Ressourcen als Icons für die Dauer dargesetllt
- Nur Berechtigungen anfragen, wenn sie auch gerade gebraucht werden
- keine Anfragen in einem Haufen
- `ckeck-SelfPermission()` und `requestPermission()`
- Erste Erklärung, dann Anfrage mit `shouldShowRequestPermissionRationale()`

## Sichere Interaktion von Apps und Schutz von App-Komponenten
- Kommuniktaion zwischen Apps per 
    - Intents
        - Nachrichten-Objekte (Wie Hülle für eine Intetion)
        - Kann eine Activity oder Service starten
        - auch als Broadcast an registrierte BroadcastReceiver
        - im Manifest kann ein Intent-Filter angegeben werden
        - Ergebnis wird auch als Intent zurückgeschickt
        - könnte auch wie ein Verteiltese System aggieren
    - RPC-Aufruf
        - nur auf einem System
        - durch IPC und einer Binder-Middleware
- `android:permission` Schutz vor Zugriffen
    - bei Fehlerhaften Zugriff: *SecurityException*
        - nicht bei Broadcast
    - *Activity*: Beschränkt, wer die Activity starten darf
    - *Service*: Beschränkt, wer den Service starten, stoppen und verbinden darf
    - *BroadcastReceiver*: Beschränkt, wer Broadcasts an den Receiver senden darf
    - *ContentProvider*: Beschränkt Lese- und Schreibrechte für die Daten im ContentProviders
- Intents:
    - keine eigene Gestaltung und Implementierung des grafischen Interfaces
    - nutzt die Standard-App des Nutzers für Aktionen
    - Nachteile sind potenzielle Auswahldialoge, Abhängigkeit von Drittanbietern, und ein gestörtes Look-and-Feel
    - Geeignet für einfache, standardisierte Aktionen
- Eigenimplementierung:
    - volle Kontrolle über den Prozess
    - ermöglicht ein einheitliches Look-and-Feel
    - gewährt Berechtigungen nur einmal
    - Geeignet für kritische Kernfunktionen mit App-spezifischen Abhängigkeiten
- Explizite Intents:
    - Ansprechen des Ziels (Activity, Service, etc.) über seinen Paketnamen oder vollqualifizierten Klassennamen
    - innerhalb der eigenen App bestimmte Komponenten zu starten
    - Sicherheit ist unproblematisch, da der Entwickler genau weiß, welche Komponente angesprochen wird.
- Implizite Intents:
    - Definieren nur eine Aktion, die ausgeführt werden soll, ohne ein spezifisches Ziel.
    - Andere Apps können darauf reagieren, sofern sie eine passende Komponente für die angegebene Aktion bereitstellen
    - Es ist wichtig, Sicherheitsmaßnahmen zu ergreifen, da sensible Daten an eine fremde App gesendet werden könnten
        - App Chooser: eine Auswahl möglicher Ziel-Apps
- Schutz vor Zugriff durch andere Apps:
    - `android:permission` -> Berechtigung für den Zugreifers muss vorhanden sein
    - Intent-Filter -> Welche Implizierte Intents empfangen werden können
    - `android:exported` -> kann die Komponente überhaupt angesprochen werden

## Rechteausweitung
- horizontale Rechteausweitung
    - Ausnutzen der Rechte von anderen Apps
- vertikale Rechteausweitung
    - mehr Rechte selber erhalten, als benötigt
    - meist über Sicherheitslücken
- Augenutzte App: *confused deputy*
- Problem, wenn nicht die Rechte der Ursprungsapp kontrolliert werden
    - `checkCallingPermission()` -> hat Urpsrungsapp die Rechte?
    - `checkCallingOrSelfPermission()` -> hat Urpsung oder ich die Rechte? (Möglicher Fehler)
    - 

## Zusammenfassung
- Systemänderungen: Zunahme der Berechtigungen durch neue Hardware-Features und Umstrukturierungen im System.
- Nutzerinteraktion: Feingranulare Berechtigungsvergabe zur Laufzeit ermöglicht, kann aber durch häufige Dialoge und unklare Erklärungen die Nutzererfahrung beeinträchtigen.
- Zielkonflikte: Balance zwischen einfacher Bedienbarkeit und hoher Sicherheit bleibt eine Herausforderung.
- Automatische Rechtevergabe: Rechte der Kategorie „normal“ und Zero-Permissions werden bei Installation automatisch gewährt, was Profilbildung und Datenpreisgabe begünstigen kann.
- App-Kommunikation: Berechtigungen schützen App-Komponenten vor unerlaubtem Zugriff. Horizontale Rechteausweitung ist möglich, erfordert jedoch Gegenmaßnahmen

# Analyse von Andriod Applikationen

## Vorgehensweise bei statischen Analyse von Android Applikationen
1. Beschaffen der Applikation
    - von Playstore
        - mithilfe einer gefakten API `https://github.com/egirault/googleplay-api`
    - von Mobil-Telefon
        - `adb shell pm list packages | grep magicsms` -> `package: com.magicsms.own`
        - `adb shell pm path com.magicsms.own` -> `package: /data/app/com.magicsms.own-1.apk`
        - `adb pull /data/app/com.magicsms.own-1.apk` -> Datei ist da
2. Entpacken der apk-Datei
3. Auslesen des Manifests
    - `aapt d permissons maleware.apk`  -> Zeigt genutzte Rechte an
    - Hat sie Berechtigungen, die sie nicht benögitgt?
    - Fehlen Berechtigungen, die sie benötigen sollte?
4. Dalvik-Bytecode in Java Bytecode
5. Umwandeln Java-Bytecode in Java-Code (o. Jimple o. smali)
6. Analysieren des generierten Codes

## Werkzeuge zur statischen Analyse

## typsiceh Sicherheitsprobleme von Android Apps

## Ablauf forensischen Analysen von Android-Geräten und deren Hindernisse




