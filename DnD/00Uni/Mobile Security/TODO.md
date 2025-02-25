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
![[Android]]
### iOS
![[iOS]]

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
![[Android]]
### IOS

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




