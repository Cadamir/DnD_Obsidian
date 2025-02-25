# Betriebssystem
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

