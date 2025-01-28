# Betriebssystem
- von Google auf Linuxbasis
- Open Source
- GooglePlay = zentraler Verteiler
- freie Installation möglich (mit AndroidPackage .apk)
- Software beschrieben durch Manifestdatei AndroidManifest.xml
    - enthaltene Komponenten
    - Hardwareeig## Sicherheitsbezogene Einstellungen
eneschaften für Ausführung
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

# Berechtigungskonzept Android
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
- Schutzmaßnahmen notwendig um Kommunikation zu schützen