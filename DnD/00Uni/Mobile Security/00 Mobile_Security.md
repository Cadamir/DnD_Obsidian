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
|Parteil|Hauptinteresse|
|-|-|
|Benutzer|Daten sicher, Gerät funktioniert|
|Hardware-Hersteller|Rechtliche Richtlinien werden eingehalten, International Mobile Equipment Identifier (IMEI) darf nicht veränderlich sein
|Netzbetreiber|Benutzer soll das eigene Netz nutzen und eigene Services und keine Drittanbieter
|Software-Entwickler/Dienstanbieter|Alle Apps installierbar, Apps nicht kopierbar
|Betriebssystem-Anbieter|Gute Basis, damit das Handy weiter genutzt wird; bietet Grundlage für Apps
|Marktplatzbetreiber|viele Apps und Käufe durch den Nutzer, Schutz des Nutzers durch Qualitätskontrollen an den Apps
|Administratoren|Sicherheit der Daten (besonders Firmendaten), unvorsichtige Benutzer ist die größte Gefahr

## Riskantes Nutzerverhalten
- kein Gefahrenbewusstsein
- geringes Wissen über Schutzmaßnahmen
- Berechtigungsanfragen durch Apps einfach akzeptiert
- Apps aus unbekannten Quellen einfach installiert (trotz Warnung)
- keine Datenverschlüsselung durch Nutzer
- Geräteeigene Dienste werden gedankenlos verwendet

## Allgemeine Sicherheitsmechanismane
 ![[Schichtenarchitektur]]
 ![[Platform Security Architecture]]
![[Softwareverteilung]]

### Mangement
- Trennung dienstlicher und privater Daten
- Nutzung von EMM (Enterprise Mobility Management) für schnelle Betreuung vieler Geräte (zB. Intallationsrechte, Löschungsrechte, ...)

## Betriebssysteme
![[Android]]

### iOS
![[iOS]]

## weitere Maßnahmen
1. [[Prävention]]
2. [[Überwachung]]
3. [[Schadensminderung]]
4. [[sichere Einstellungen]]


![[OWASP Top 10]]


# Analyse der Zugriffe auf sensible Nutzerdaten
- [[sensible Android Berechtigungen]] betrachten
- in *[[smali]]* und dann `grep`
- in [[Codeinspekt]]

- [[DroidBox]] und [[CuckooDroid]] helfen bei Berechtigungssuche
	- zB. nach Adressbuch oder Kontakte
	- [[Frida]] & [[Drozer]] sind alternativen
- Exportierte [[Acitivies]] können fehlerhafte Authentifizierung haben
- Exportierte Content Provider und SQL-Injections
    - mit [[Drozer]]
    - `dz> run app.provider.info -a com.mwr.example.sieve -u`
# Schwachstellen bei JavaScript-Interface
- Webseitenanzeige innerhalb der App: *WebView*
- Viele Funktionen (Dateizugriff, Plugin laden, Passwortspeicher)
- Funktion: `addJavascriptInterface` -> Aus der Webseite heraus Funktionen eine Funktion aufrufen -> Webseitenbetreiber oder Man in the Middle können verschiedene Funktionen einfach so aufrufen

# Ungewollter Datenabfluss durch Backup-Funktion
- jeder kann seine App mit Backups durch Android ausstatten
- dafür sind keine besonderen Rechte notwendig
- Backup auf ein System, dass keine Sandboxes oder Rechtekontrollen mehr hat
    `adb backup -apk -shared <paketname>`
- Datei `backup.ab`
- mit *Android Backup Extractor* analysierbar
- jetzt ist zB die Datenbank ungesichert auf dem Rechner des Angreifers

# Spurensuche im Dateisystem
- Vollverschlüsselung umgehen:
    - *Full Disk Encryption (FDE)*
    - beruht auf *dmcrypt* und wird in der *data*-Partition auf Nutzerdaten angewendet
    - Jedes Schreiben verschlüsselt, jedes Lesen entschlüsselt
    - Schlüssel sind zufällige 128Bit, die durch den Screenlock-Pin geschützt werden
    - *Crypto Footer* speichert wichtigstes
        - nur ein Passwort wird gespeichert
        - keine Zerlegung des Schlüssels im Speicher
        - Keine Verifizierung durch Signatur (da immer entschlüsselt wird, indirekte Verifikation)
        - *FDE-Schema-Verison*
        - *Schlüssellänge*
        - **Verschlüsselungsalgorithmus (aes-dbd-essiv:sha256)*
        - brechen:
            - Verschlüsselte Partition finden + Crypto-Footer (`fstab`) -> Eigenschaft `encryptable`
            - 
- aktuelle Android-Endgeräte nicht leistungsfähig -> Brute-Force-Angriff gegen Verschlüsselung
- 1. die Partition finden, die den Crypto Footer hat
    -  `fstab` -> Partition, mit Eigenschaft *encryptable*
    - Partition (mit Daten und Crypto Footer) kopieren auf Analyserechner (`adb shell dd if=/dev/block/mmcblk0p13 of=tmp_footer` & `adb pull tmp_footer`)
    - Teilmenge der Nutzer-Partition holen:  `adb shell dd if=/dev/block/mmcblk0p12 of=tmp_header bs=512 count=1` & `adb pull tmp_header` 
    - Angriff mit *Hashcat* o. Python-Skript (`python crypto_footer.py tmp_header tmp_footer (maximale PIN-Länge)`)

### Bildschrimsperre angreifen
- fünf Arten der Bildschirmsperre:
1. Gesichtserkennung
2. Muster
3. PIN
4. Passwort
5. Fingerabdruck

- sehr stark auf Benutzbarkeit ausgerichtet -> alle PINs oder Passwörter identisch sind.
- PIN und Passwort ist identisch
- Berechnung des Musters
    - min. 4 Punkte auf einer 3x3 (4x4) Matrix verbindet ohne Wiederholung
    - Jeder Punkt hat einen Zahlenwert (0-8)
    - gespeichert wird die Reihenfolge
    - Speicher-Datei `gesture.key` in `/data/system/`
    - mit SHA1 gehashed(zB. c8c0b24a15dc8bbfd411427973574695230458f0)
    - Problem: Nur Reihenfolge bildet Hash -> mit "Rainbow-Table" (vorausgerechneten Kombinationen) lässt sich das Passwort rauskriegen
    - Zugriff auf die Datei
        1. Mittels Root-Exploit: -> Wenn *root*, dann einfach auslesen
        2. Mittels JTAG: 
            - auf den Speicher zuzugreifen
            - suche nach: 
                - 2048 Byte langen Abschnitt
                - mit 20 Byte Zufallswerten beginnt
                - danach 2012 Byte mit Nullen 
                - am Ende wieder 16 Byte Zufallswerte
            - die ersten 20 Byte -> unser gesuchter Hashwert
- per PIN oder Passwort
    - wie zuvor, folgend ein paar Unterschiede
    - Speicher-Datei: `password.key` in `/data/system`
    - Hashwert-Berechnung mit Salt
    - Salt-Datei: `/data/system/locksettings.db`
        - früher: /data/data/com.android.providers.settings/databases/settings.db und auslesbar durch: `SELECT value FROM locksettings WHERE name=’lockscreen.password_salt’`
    - `hashcat -a 3 -m 110 <password-Hash>:<Hexadezimal-Salt> -1 ?d ?1?1?1?1?1?1?1?1` (max. 8-stellige PIN)
    - kein Root-Zugriff: JTAG
        - Suchen: 
            - 2048 Byte langen Abschnitt
            - 72 Byte, die nur aus den Zeichen 0–9 und A–F bestehen
            - 1960 Byte mit Nullen
            - 16 Byte Zufallswerte
        - ersten 72 Byte: Hashwerte unserer PIN/Passworts als MD5 und SHA1
        - Salz: 
            - Im Speicher suchen nach String `lockscreen.password_salt`
            - Byte direkt vor String muss im Bereich 0x0F und 0x35 -> länge des Hashwertes
            - Ein weiteres Byte davor muss Byte 0x3D
            - Direkt davor muss ein Byte mit Nullwerten stehen

### Nach interessanten Daten im Dateisystem suchen
- Interessante Orte
    1. Die installierten Applikationen: `/data/apps/`
    2. Datenbanken und Dokumente, die durch die Applikation bearbeitet und verwaltet werden: `/data/data/<paketname>`
    3. die SD-Karte (jede Applikation mit Recht hat Zugriff)
- Interesse: Datenbanken & shared_preferences
- Applikationsdatenbanken:
    - SQLite-Datenbanken 
    - Verzeichnis /data/data/<paketname>/databases/
    - Nutzer-, Applikations-, Konfigurationsdaten
    - grafische Datenbank-Tools helfen für schnellen Überblick
- Shared Preferences
    - XML-Dateien mit Key-Value-Paare
    - Eigenschaft: private und shared
    - shared: jede Applikation darauf zugreifen
    - oft Schlüsselmaterial für die Verschlüsselung der Datenbankinhalte, Passwörter für Nutzer der Applikation oder URLs und IP-Adressen der Backend-Systeme
    - durch löschen kann "interessantes" Fehlerverhalten provoziert werden
- Weitere Dateien innerhalb der Sandbox
    - /data/data/<paketname>/files
	
	
## Android Appliatkionen zur Laufzeit manipulieren

- Debugging  bzw.  das  Manipulieren  von  Variablen  zur  Laufzeit  
- spezielles Tool (Debugger)
    -  IDA Pro
    - **Codeinspect**
    - eingebaute Debugger in Eclipse oder dem Android Studio
- Hooken durch Debugger im Android-Manifest zu verhindern -> `android:debuggable="false"`
    - Lösung 1: Flag überschreiben durch Dekompilieren im Vorraus
    - ein Hooker der zur Laufzeit das Flag von "false" auf "true" setzt
        *XPosed Framework* mit *BuildProp Enhancer*
- Debugging und Breakpoints
- Dynamisches Änderung von Variablen
- Steuerung des Emulators
    - 
- Einfügen und Kompilieren von Code
    - Logausgaben
    - Daten abfangen
- Forensische Analyse und Sicherheitsprüfung
    - Unsachgemäße nutzung von Personenbezogenen Daten
    - mögliche Exploits

# Kapitel 5 - Reversing von iOS-Apps
## Lernziele:
- die unterschiedlichen Vorgehensweisen bei der statischen Analyse von
iOS-Applikationen zu kennen,
- die dazu nötigen wichtigen Werkzeuge benennen und ihren spezifischen
Einsatzzweck erklären zu können,
- die Techniken bei der Analyse und Manipulation von iOS-Applikationen
zur Laufzeit beschreiben zu können,
- wichtige Orte im Dateisystem bei der forensischen Analyse von iOS-Geräten benennen zu können und wie auf diese zugegriffen werden kann
und
- systemeigene Sicherheitsfunktionen von iOS, die Applikationen bereitgestellt werden, benennen zu können und deren Zweck zu kennen

## Vorgehen beim Reversing
1. Beschaffen der zu untersuchenden Applikation
2. Analysieren des Codes

## Wichtige Tools und Helfer
-  Cydia, SSH, libimobiledevice
### Cydia
- alternativer Marktplatz
    - für jailbroken iOS-Geräte (Betriebssystem modifiziert)
    - mit Pangu2 oder TaiG3 beim Jailbreak installiert
    - Apps im deb-Format
    - von einer Vielzahl an frei wählbaren Quellen

### SSH und SCP
- per SSH (Secure Shell) verbinden
- in Cydia das Paket OpenSSH
- **ssh** -> `ssh root@<ios_device_ip>` -> Verbindung zum iOS-Gerät
- **scp** -> `scp <pfad_der_lokalen_datei> root@<ios_device_ip>:<pfad_auf_dem_ios_device>` -> Dateien austauschen
- nach Installation neue Passwörter (sonst Standard-Passwort)
- Passwort für *root* und *mobile* = alpine

### libimobiledevice
- Standard-Verbidnung Analyserechner und Gerät -> WiFi -> langsam und Verbindugnsunsicher
- SSH- o. Debugger-Verbindung über USB
- `$: brew install libimobiledevice` -> Installation
- `$: iproxy 2222 22` -> Analyserechnerport 2222 ist gleich Geräte-Port 22
- `$: ssh root@localhost -p 2222` -> SSH-Verbindung zum angeschlossenen iPhone
- `$: ssh root@localhost -p 2222 'mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys' < ~/.ssh/id_rsa.pub` -> SSH-Public-Key auf iPhone um zukünftige
Passwortabfragen bei SSH zu vermeiden:

### class-dump
- Kommandozeilentool *class-dump*
- analysiert Objective-C-Runtime-Informationen aus Mach-O-Dateien
- extrahiert sämtliche Header der enthaltenen Class-Dateien
- Syntax sehr nah an Originaldatei
- erzeugt .h-Dateien -> C-Editoren
- Syntax von class-dump einfach
- Befehle
    - `-a` show instance variable offsets
    - `-A` show implementation addresses
    - `--arch <arch>` choose a specific architecture from a universal binary
(ppc, ppc64, i386, x86_64)
    - `C <regex>` only display classes matching regular expression
    - `-f <str>` find string in method name
    - `-H` generate header files in current directory, or directory specified with -o
    - `-I` sort classes, categories, and protocols by inheritance (overrides -s)
    - `-o <dir>` output directory used for -H
    - `-r` recursively expand frameworks and fixed VM shared libraries
    - `-s` sort classes and categories by name
    - `-S` sort methods by name
    - `-t` suppress header in output, for testing
    - `--list-arches` list the arches in the file, then exit
    - `--sdk-ios` specify iOS SDK version (will look in /Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS<version>.sdk
    - `--sdk-mac` specify Mac OS X version (will look in /Developer/SDKs/MacOSX<version>.sdk
    - `--sdk-root` specify the full SDK root path (or use --sdk-ios/--sdk-macfor a shortcut)
- alle Header extrahieren:
    - Suche des eigentlichen Executables unserer Applikation -> `$: plutil -p Test.app/Info.plist | grep CFBundleExecutable "CFBundleExecutable" => "Test"`
    - Extrahieren der Header in das Verzeichnis out: -> `$: class-dump -S -s -H Test.app/Test -o out`
    - in `out` -> alle Class-Datei-Header
    - grobe Aufbau erkennbar

### dumpdecrypted
- Apps sind verschlüsselt
- dumpedcrypted muss für das installieren erst kompiliert werden
    - Download von github
    - kompilieren mit c
- `dumpdecrypted.dylib` -> Programm

### lipo
- Prozessorarchitekturen auslesen
- kann Architekturen entfernen
    - manche Tools unterstützen bestimmte Architekturen nicht (oft bei *arm*)
-  `$: lipo -info Test.decrypted`
- `$: lipo -thin armv7 -output Test-v7.decrypted Test.decrypted`

### cycript
- interaktive Konsole
- andocken an jeden aktuell laufenden Prozess/App
- dann manipulieren bzw. interagieren
- Mischung aus der Objective-C++ und der JavaScript-Syntax
- Paket cycript installieren
- cycript nach ssh-Verbindung starten:

> `$: cycript`

> `cy# var alert = [[UIAlertView alloc] initWithTitle:@"Hacking Mobile" message:@"Ein herzliches ''Hallo'' an den Analysten!" delegate:nil cancelButtonTitle:@"OK" otherButtonTitles:nil]`

> `#"<UIAlertView: 0x1652c5b0; frame = (0 0; 0 0); layer = <CALayer: 0x18acdd60>>"`

> `cy# [alert show]`

> `# Beenden der cycript-Konsole:`

> `ctrl + d`

-  direkt Schwachstellen erkennen und auszunutzen

## Beschaffen der zu untersuchenden Applikation
1. Auffinden der Applikation selbst
2. Auffinden des Dokumentenverzeichnisses der Applikation
3. Entschlüsseln der Applikation
4. Übertragen der Applikation auf den Analyserechner

### Auffinden der Applikation selbst
- `/var/mobile/Containers/Bundle/Application/<unique_id>`
- Problem: **unique_id**
- gewünschte Applikation starten -> per SSH -> `ps` nach App-Prozess -> Verzeichnis
> `$: ps -e | grep Test.app`

> `1392 ?? 0:02.38 /var/mobile/Containers/Bundle/Application/DFA9B5E3-0DD4-438E
-94D1-17FF70189B43/Test.app/Test`

- sonst nach `.app/` suchen
  - viele Ergebnisse -> richtiges suchen#
  - `ps -e | grep .app`

### Auffinden des Dokumentenverzeichnisses der Applikation
- zugehörige Dokumentenverzeichnis der Applikation
- zum Entschlüsseln des Executables benötigt

> `$: cycript -p Test`

> `cy# [[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory
inDomains:NSUserDomainMask][0]
#"file:///var/mobile/Containers/Data/Application/2B3B8593-1B03-4148-A132-
B52BC1439D52/Documents/"`

-ab IOS9 : `/var/root/` 

### Entschlüsseln der Applikationen
- dumpdecrypted.dylib auf das iOS-Gerät kopieren

> `$: scp -P 2222 dumpdecrypted.dylib root@localhost:/var/mobile/Containers/Data
/Application/2B3B8593-1B03-4148-A132-B52BC1439D52/Documents/
dumpdecrypted.dylib 100% 193KB 192.9KB/s 00:00`

- per SSH verbinden
> `$: DYLD_INSERT_LIBRARIES=dumpdecrypted.dylib /var/mobile/Containers/Bundle/
Application/DFA9B5E3-0DD4-438E-94D1-17FF70189B43/Test.app/Test
mach-o decryption dumper`
- wenn alles funktioniert: `Test.decrypted`

### Übertragen der Applikation auf den Analyserechner
- mit `scp` auf Analyserechner
- mit Decompiler weiter analysieren
  -  oder Tools *otool* und *class-dump*
## Werkzeuge zum Analysieren der Applikationen
- Reversing bei iOS deutlich aufwendiger
  - nach Assembly

### Hopper
- zum disasamblen
- sehr ähnlich zu IDA Pro
- für 32/64Bit-Intel-Mac-, Windows- und iOS-ARM
- Aufbau
  - Links: Übersicht aller gefundenen Labels und Strings und der Suchfunktion
  - Mitte: Prozessorinstruktionen und zugehörige Speicheradressen
  - Rechts: Metainformationen über das Executable
  - Oben: Die Menüleiste mit den wichtigsten Shortcuts von Hopper
- Beim Auswerten:
  - Die Speicheradresse
  - Die Prozessorinstruktionen in Assembly
  - Und mit das Wichtigste: der Kommentar mit oft sehr hilfreichen Hinweisen
- Durch Button "if(b) f(x)"
  - Übesetzung in Pseudo-Code
  - Pseudo-Code weicht deutlich vom Original-Code ab
- Control-Flow-Graphen (CFG)
  - gut um Ablauf zu verstehen
  - kann sehr groß werden
- Debuggen von Applikationen

### IDA Pro
- Industriestandard
- Aufbau:
  - Links: Übersicht aller gefundenen Funktionen innerhalb der dekompilierten Applikation
  - Links unten: Die Graphendarstellung mit dem Programmfluss
  - Mitte: Prozessorinstruktionen (Graphendarstellung)
  - Oben: Die Menüleiste mit den wichtigsten Shortcuts und den verschiedenen Fenstern
- Suche nach *ViewController* mit Namen passenden Namen 
- sehr hilfreich für erstem Überblick
- Anbindung eigener API an externe Python-Skripte mittels IDAPython

# Kapitel 6
Die Lernziele dieser Lektion sind:
• die typischen Varianten von Man-in-the-Middle-Angriffen auf den Netzerkverkehr mobiler Applikationen zu kennen und unterscheiden zu
können,
• zu wissen, wie diese Angriffe technisch durchführbar sind und welche
Werkzeuge hierbei eingesetzt werden können,
• die Grundidee von SSL-Stripping-Angriffen erklären zu können und
• abgefangene Datenpakete obigen Netzwerkverkehrs analysieren und interpretieren zu können.

# 6 Sicherheitsprobleme bei iOS-Applikationen
## 6.1 Wichtige Tools und Helfer
- Wichtige Tools
  - otool
  - Snoop-it
  - frida
- otool
  - ähnlich zu class-dump
  - ließt zusätzlich noch die verwendeten Bibliotheken aus
> `$: otool -L Test.app/Test`
>
> `Test.app/Test:
>
> /System/Library/Frameworks/QuartzCore.framework/QuartzCore (
compatibility version 1.2.0, current version 1.8.0)
>
> /System/Library/Frameworks/CoreGraphics.framework/CoreGraphics (
compatibility version 64.0.0, current version 600.0.0)
>
> ...
>
> /usr/lib/libobjc.A.dylib (compatibility version 1.0.0, current
version 228.0.0)
>
> /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current
version 173.8.0)
>
> /System/Library/Frameworks/CoreFoundation.framework/CoreFoundation (
compatibility version 150.0.0, current version 793.0.0)`

- Snoop-it
  - Library Injection 
  - API Hooking zur Laufzeit
  - durch das Einschleusen einer Bibliothek -> App bekommt Überwachungsfunktionen
  - Aufzeichnung im Hintergrund von zentrale sicherheits- und privatsphärerelevante Vorgänge
  - Start von Webserver 
    - Interaktion zwischen Analyserechner und App
    - Webbasierte Nutzerobfläche
  - Bereitstellung einer XML-RPC-Schnittstelle 
    - Echtzeitzugriff auf aufgezeichnete Ergebnisse
    - Echtzeitzugriff auf Analysefunktionen
  - eine App (über Cydia)
    - Kommunikationsport
    - zu überwachende App
    - URL
    - Certificate Pinning umgehen
    - Jailbreak-Detection umgehen
  - Aufzeichnung Dateisystemzugriffen inkl. der NSFile-Protection-Klasse
  - Zugriffe auf Keychain inkl. der verwendeten kSecAttr-Klasse
  - Zugriffe auf sensible und privatsphärerelevante APIs
  - Verwendung der Crypto-APIs
  - Die Möglichkeit zur Manipulation von Gerätedaten
- idb
  - Installation auf dem Analyserechner
  - Verbindung auf Smartphone mit USB
  - mögliche fehlende Pakete können direkt über idb installiert werden
  - Auswahl der Test-App
  - Aufzeichnung Dateisystemzugriffen inkl. der verwendeten NSFileProtection-Klasse
  - Zugriffe auf die Keychain inkl. der verwendeten kSecAttr-Klasse
  - Binary-Analysen wie z. B. Prüfen, ob das Binary verschlüsselt ist oder ob Techniken wie ASLR, DEP oder ARC implementiert sind
  - Strings aus der Applikation extrahieren
  - URL-Handlers auflisten
  - Die Zwischenablage überwachen
  - Nach Snapshots der Applikation suchen
- frida
  - ähnlich wie cycript
  - Laufzeitmanipulation
  - Hooken von Funktionsaufrufen
  - Manipulation von Funktionsaufrufen mittels JavaScript
  - automatisierbar (durch eigene Skripts)
  - Installation auf Analyserechner und iOS-Endgerät mittels app
    - Cydia-Repository: http://build.frida.re/
    - gleiche Version auf beiden Geräten notwendig
  - wichtigste Befehle
    - `frida-ps -Ua` -> alle aktuell laufenden Apps
    - `fieda-ps -Uai` -> alle installierten Apps
    - `firda-trace -i "resc*" -i "send*" Safari` -> Überwacht recv* und send* APIs in Safari
    - `frida-trace -m "-[NSView drawRect:]" Twitter` -> Überwacht ObjC-Methodenaufrufe in Twitter
    - `frida -U Notizen` -> Verbinden von frida mit der laufenden Notizen-Applikation zum Manipulieren
- Keychain-Dumper
  - alle Einträge in der Keychain auszulesen
  - was eine bestimmte Applikation in der Keychain ablegt
  - Einschränkung durch Parameter
    - `Usage: keychain_dumper [-e]|[-h]|[-agnick]`
    - <no flags>: Dump Password Keychain Items (Generic Password, Internet Passwords)
    - -a: Dump All Keychain Items (Generic Passwords, Internet Passwords,Identities, Certificates, and Keys)
    - -e: Dump Entitlements
    - -g: Dump Generic Passwords
    - -n: Dump Internet Passwords
    - -i: Dump Identities
    - -c: Dump Certificates
    - -k: Dump Keys
- FileDP
  - Schutzklassen eine Datei oder ein ganzes Unterverzeichnis einer zu untersuchenden Applikation besitzt
> `$: ./FileDP -d /var/mobile/Containers/Data/Application/07DF1A1E-DD3B-45D8-A55B-385FAC05C2BF`
>
> `2016-03-12 17:41:02.788 FileDP[1201:205901] file name is:/var/mobile/
Containers/Data/Application/07DF1A1E-DD3B-45D8-A55B-385FAC05C2BF/.com.
apple.mobile_container_manager.metadata.plist - protection class:
NSFileProtectionNone`
>
>...

## 6.2 Analyse der Zugrie auf sensible Nutzerdaten
- nur die Daten verlassen mein Handy, die es auch sollen
- Snoop-it
  - Anzeige aller API-Zugriffe
    - Monitoring → Sensitive API
    - zB. Internet-API und Kamera-API

## 6.3 iOS-Applikationen zur Laufzeit manipulieren
- frida oder Theos
- Theos#
  - Cross-Plattform-Toolsammlung
  - Erweiterungen (Tweaks) für iOS-Applikationen zu schreiben
  - Tweaks auf Endgerät ausführen
  - Installation auf Analyserechner
  - Installation via Cydia
    - http://coolstar.org/publicrepo und http://nix.howett.net/theos
    - zusätzlich folgende Pakte: Perl, Theos, iOS Toolchain
  - Logos = Reihen von Befehlen
    - eigene Hooking-Scripte
    - `%hook`: Ist der Beginn eines Hookings für eine definierte Klasse nnerhalb der zu analysierenden Applikation
    - `%orig`: Ruft die eigentliche Implementation der gehookten Klasse auf
    - `%log`: Schreibt die Argumente, mit denen eine gehookte Methode aufgerufen wurde, in das Systemlog.
    - `%end`: Ist das Ende eines Hookings für eine definierte Klasse innerhalb der zu analysierenden Applikation
  - Umgehen der Jailbreak-Erkennung
    - Katz-und-Maus-Spiel
    - Bsp Erkennung
      - Suche nach Cydia
      - Ausführen von eigentlich nicht vorhandenen Befehlen ("fork()")
    - ähnlich wie Reversing
      - installieren App auf Testgerät
      - dump_decrypted (entschlüsseln)
      - `otool -ov` -> Suche nach auffällige Methoden- oder Klassennamen 
      - kein Erfolg -> Vollständiges Reversing mit Hopper/IDA Pro
      - Erfolg -> Umgehen zB. mit Theos und Logos

> $: perl /var/theos/bin/nic.pl
> 
> NIC 2.0 - New Instance Creator
> 
> ------------------------------
>
> [1.] iphone/activator_event
> 
> [2.] iphone/application_modern
> 
> [3.] iphone/cydget
> 
> [4.] iphone/flipswitch_switch
> 
> [5.] iphone/framework
> 
> [6.] iphone/ios7_notification_center_widget
> 
> [7.] iphone/library
> 
> [8.] iphone/notification_center_widget
> 
> [9.] iphone/preference_bundle_modern
> 
> [10.] iphone/tool
> 
> [11.] iphone/tweak
> 
> [12.] iphone/xpc_service
> 
> Choose a Template (required): 11
> 
> Project Name (required): jbobfuscator
> 
> Package Name [com.yourcompany.jbobfuscator]: de.mspreitz.jbobfuscator
> 
> Author/Maintainer Name [Michael Spreitzenbarth]:
> 
> [iphone/tweak] MobileSubstrate Bundle filter [com.apple.springboard]:
> 
> [iphone/tweak] List of applications to terminate upon installation 
> 
> spaceseparated, '-' for none) [SpringBoard]:
> 
> Instantiating iphone/tweak in jbobfuscator/...
> 
> Done.

- Erzeugung des Tweaks "jbobfuscator" mit **nic.pl**

> %hook SecurityChecks
> - (BOOL) isDeviceJailbroken {
> return NO;
> }
> %end
> 

  - Befehl `fork` muss -1 zurückgeben -> Tweak erstellen der fork aus einer Applikation heraus -1 zurückgibt

> #include <sys/types.h>
>
> #include <unistd.h>
>
>static pid_t (*original_fork)(void);
>
>pid_t new_fork(void) {
>
>return -1;
>
>}
>
>%ctor{MSHookFunction((void*)fork,(void*)new_fork,(void**)&original_fork);}


- tweaks mit "make" kompilieren -> erzeugten Dynamic Libraries nach /Library/MobileSubstrate/DynamicLibraries/

- an mehreren Stellen Prüfroutinen für Jailbreak -> Angreifer schwer machen

## 6.3.2 Umgehen der Applikations-PIN
1. Umgehen des Bildschirms zur PIN-Abfrage
   - nur wenn Daten nicht verschlüsselt sind
   - keine spätere Prüfung des PINs
   - Auffinden der App -> Entschlüsseln -> Download auf Analyserechner -> Detektiv-Arbeit (class-dump) -> mit cycript der `UIApp.keyWindow-Variable = MainView` 


- Bsp:
> $: ps aux | grep Password\ Keeper\ Lite
> 
> mobile 793 17.8 4.2 646236 43688 ?? Ss 5:42PM 0:04.03 /var/mobile/Containers/Bundle/Application/7AA5C9A9-633A-462E-8479-8C4952C79F84/Password Keeper Lite.app/Password Keeper Lite
>
> #. nun verbinden wir cycript mit der laufenden Applikation
>
> $: cycript -p 793
> 
> cy:
>
> #. in diesem Schritt teilen wir der Applikation mit, dass sie uns die MainView anzeigen soll und nicht die PIN-Abfrage
>
> cy: UIApp.keyWindow.rootViewController = [[MainView alloc] init]; "<MainView: 0x5422ed>"
>
> cy:

2. Überschreiben der PIN
  - mit *Snoop-it* oder *frida*
  - starten Interaktive Python-Shell
  - Import von Frida-Bindings
  - Codefrakment in JavaScript das von Frida ausgeführt werden soll
  - Script mit Prozess Koppeln
    - frida-ps -U
  - script ausführen

> Python 2.7.10 (default, Oct 23 2015, 19:19:21)
>
> [GCC 4.2.1 Compatible Apple LLVM 7.0.0 (clang-700.0.59.5)] on darwin
>
> Type "help", "copyright", "credits" or "license" for more information.
>
> .>>> import frida,sys
>
> .>>> frida.get_device_manager().enumerate_devices()
>
> [Device(id="local", name="Local System", type='local'),
> 
> Device(id="tcp", name="Local TCP", type='remote'),
> 
> Device(id="460683e35xxxx02526d", name="iPhone", type='tether')]
> 
> .>>> jscode = """
>
> ... var AppDelegate = ObjC.use("AppDelegate");
>
> ... AppDelegate.newPassword_("1111");
>
> ... """
>
> .>>> process = frida.get_device_manager().enumerate_devices()[2].attach(793)
>
> .>>> session = process.session
> 
> .>>> script = session.create_script(jscode)
> 
> .>>> script.load()

1. Brute-Forcing der PIN im Hintergrund
- sinvoll wenn mehrere Apps mit PIN gesperrt sind
- *cycript* und *class-dump*
- Pin-Test-Script erstellen (siehe unten)
  - Schleife um alle 10.000 PINs zu testen
  - Wenn vorhanden Zähler falscher Versuche zurücksetzen
> #. zuerst benötigen wir die PID der zu analysierenden Applikation
>
> $: ps aux | grep Password\ Keeper\ Lite mobile 793 17.8 4.2 646236 43688 ?? Ss 5:42PM 0:04.03 /var/mobile/Containers/Bundle/Application/7AA5C9A9-633A-462E-8479-8C4952C79F84/Password Keeper Lite.app/Password Keeper Lite
>
> #. nun verbinden wir cycript mit der laufenden Applikation
>
> $: cycript -p 793
>
> cy:
>
> #. in diesem Schritt prüfen wir eine PIN
>
> cy: [UIApp.delegate checkPassword:"4711"]
>
> 0
>
> cy:


## 6.4 Spurensuche im Dateisystem
- kein Dateizugriff ohne Nutzermitarbeit
  - PIN muss bekannt sein
- Interessant
  - Keychain
  - Snapshots der Applikation
  - Cache-Dateien des Betriebssystems

### 6.4.1 Die Keychain und ihre Daten
- *Keychain-Dumper*
  - alle dort hinterlegten Passwörtern, Nutzernamen und Zertifikaten
  - oft auch PIN
- Schutzklasse von Dateien überprüfen (*snoop-it* & *idb*)
  - immer höchstmöglichen Schutz
  - kSecAttrAccessibleWhenUnlocked

### 6.4.2 Die Applikations-Sandbox und ihre Daten
-Typen
  - Dateien
  - SQLite-Datenbanken
  - plist-Dateien
  - alles, was die Applikation noch verarbeitet
- Was speichert die Applikation in ihrer Sandbox überhaupt?
- Wie speichert sie es?
- Standard: NSFileProtectionCompleteUntilFirstUserAuthentication
- Sensible Inhalte: NSFileProtectionComplete
- Wenn man an den Inhalt kommt -> sehr cool (für Angreifer)

### 6.4.3 Der Snapshot einer Applikation
- zuletzt angezeigte Bildschirm der jeweiligen Applikation -> Snapshots
  - gespeichert innerhalb der Sandbox
  - immer wenn gerade verwendete Applikation in den Hintergrund verschoben wird
- manche Apps ersetzen SnapShot durch leeres Bild
- Speicherort: /var/mobile/Containers/Data/Application/<Application-UID>/Library/Caches/Snapshots/<Application-Package-Name> oder /var/mobile/Containers/Data/Application/<Application-UID>/Library/Caches/Snapshots/Application-Package-Name>/downscaled/ mit Endung .png
  - verschiedenen Auflösungen speichern.
- `willEnterBackground-API`  -> Snapshot durch festes Bild ersetzen
- (void)applicationDidEnterBackground:(UIApplication *)application

### 6.4.4 Die systemeigenen Logs
- NSLog
- lange Zeit gecacht + jedem zur Verfügung mit physischen iOS-Endgerät
- zB. mit *idb* 

### 6.4.5 Die Zwischenablage
- Zwischenablagen
- oft keinen Zugriffsschutz
- Typen:
  -  *UIPasteBoardNameGeneral*: 
     -  Default
     -  kopiert oder ausschneidet
     -  Inhalte öffentlich
  - UIPasteBoardNameFind: 
    - alle Werte in Suchfelder des Typs `UISearchBar`
    - Inhalte öffentlich.
  - pasteBoardWithName
    - private Zwischenablagen
    - Zugriff durch vorher definierten Gruppe
- Speicherort von allen /private/var/mobile/Library/Caches/com.apple.UIKit.pboard/ 
- Passwort-Manager sehr anfällig
- Zwischenablage bei Schließen der App löschen

### 6.4.6 Die systemeigenen Caches
- Keyboard-Cache
  - automatische Rechtschreibkorrektur 
  - speichern jedes getippte Wort (de_DE-dynamic-text.dat)
    - /var/mobile/Library/Keyboard/
  - In einem Unterverzeichnis: Nutzerwörterbuch
    - alle individuellen Wörter
    - oft Passwörter, Nutzernamen, Projekt-Namen, etc.
- HTTPResponse-Cache
  - privat
  - liegt der geschützten Sandbox der App
  - Zugriff durch Jailbreak
  - Webbasierte Apps: UIWebView
    - lokaler Cache für HTTP-Kommunikation
  - /var/mobile/Containers/Data/Application/<ApplicationUID>/Library/Caches/<Application-Package-Name>/
    - Cache.db: der systemeigene HTTP-Response-Cache der Applikation
    - *.localstorage: Offline-Storage einer HTML5-Applikation
    - file__0/*.db: Offline-Datenbanken einer HTML5-Applikation
    - Databases.db: Offline-Datenbanken einer HTML5-Applikation
  - *SQLiteBrowser* oder *File Juicer*
  - eigentlich: kein Cache oder Cache löschen bei sensiblen Daten

## 6.5 Fehlende systemeigene Sicherheitsfunktionen in iOS-Applikationen erkennen
- weglassen der iOS-Sicherheitstools
- ist eher selten

### 6.5.1 Codeverschlüsselung
- Apps vom Appstore: Raubkopierschutz
  - Digital Rights Management (DRM)
  - Verschlüsselung des Executables
- Wurde sie verschlüsselt?
- Wie können wir sie entschlüsseln?
- *otool* kann schauen, ob etwas verschlüsselt ist
  - *LC_ENCRYPTION_INFO* -> ob verschlüsselt + wo der entschlüsselte Bereich zur Laufzeit 
  - Finden der entschlüsselten App zur Laufzeit
    - mit Debugger verbinden
      - Rückgabe des Befehls `info sharedlibrary`
      - Adresse im Speicher mit dem Startpunkt der Applikation
      - Auf diese Adresse -> das ausgegebenen Offset (cryptoff) hinzurechnen
      - (cryptsize) -> Größe des Binaries
      - `dump memory` -> entschlüsseltes Binary in eine Datei schreiben
      - verschlüsseltes Binary überschreiben (`dd`)
      - *cryptid* muss auf 0 gesetzt werden
        - Suche über Hex-Editor, knifflig

### 6.5.2 Address Space Layout Randomization
- Address Space Layout Randomization (ASLR)
  - Bibliotheken, Executable & Stack und Heap in nicht vorherberechenbare Speicherbereiche geladen werden
- Applikation als Position-Independent Executable (PIE) gebaut
- Prüfung mit *otool*
  - PIE-Flag
- *idb* zeigt es direkt an

### 6.5.3 Stack-Smashing-Schutz
- Schutz vor Buffer-Overflow
- durch Stack Canaries
  - Informanten genannt
  - Marker im Speicher, die anzeigen, ob Puffer übergelaufen ist
  - App schreibt zur Laufzeit an bestimmte Stellen im Speicher vordefinierte Werte. -> Zur Laufzeit Überprüfung der Werte
  - Bei Fehler: Kein Einlesen der Variable
- Xcode verwendet LLVM-Compiler
- *otool* ->  `otool -Iv LoginNativ | grep stack`

### 6.5.4 Automatic Reference Counting
- Automatic Reference Counting (ARC) 
  - Speichermanagement durch Compiler (nicht durch Entwickler)
- kann auf Bereiche der App angewendet werden
- *idb* empfohlen
- *otool* -> `otool -Iv LoginNative | grep retain`
- Gesuchte Bezeichnungen:
  - objc_autoreleaseReturnValue
  - objc_release
  - objc_retain
  - objc_storeStrong


# 9 Angrie auf die Datenübertragung
- klassische Man-in-the-Middle-Angriffe mit (nicht-)
vertrauenswürdigen Zertifikaten
- die Umgehung des Certificate Pinning 
- SSL-Strip
## 9.1 Schutz der übermittelten Daten auf dem Transportweg
- Verschlüsselte Kommunikation
  - Secure Sockets Layer (SSL) -> Transport Layer Security (TLS)
  - TLS1.2
- hybrides Verschlüsselungsprotokoll zur sicheren Datenübertragung
  - symmetrische Verschlüsselung (beim Session-Key) und asymmetrische Verschlüsselung (beim Schlüsselaustausch) 
- der Client (App) baut Verbindung Backend-Server auf
- Backend-Server authentisiert sich mit einem X.509-Zertifikat
  - Prüfung durch Client in Bezug auf die Gültigkeit und die Übereinstimmung des Servernamens
  - nach erfolgreiche Prüfung -> Client schickt Server mit dem öffentlichen Schlüssel des Servers verschlüsselte geheime Zufallszahl oder machen den Diffie-Hellman-Schlüsselaustauschs 
  - Aus dem Geheimnis -> kryptografischer Schlüssel ableiten
  - Nachrichten der Verbindung mit einem symmetrischen Verschlüsselungsverfahren verschlüsseln

## 9.2 Man-in-the-Middle-Angriffe
- Ziel: in die Verbindung einklinken + den übermittelten Datenstrom entschlüsseln
- zwischen mobile Endgerät und Backend-Server
  - als Proxy
  - ARP-Spoofing (Router nachmachen)
- Erhalt aller Datenpakete und selbst weiterleiten
- Zertifikat notwendig
  - nicht vertrauenswürdig
  - vertrauenswürdige

### 9.2.1 Man-in-the-Middle-Angriff mit nicht vertrauenswürdigem Zertifkat
- Proxy notwendig
  - einklinken in Verbindung
  - alles mitschneiden
- *Burp Suite* verwenden
  - *mitmproxy* oder *Charles Proxy* Alternative
  - Proxy → Options → Proxy Listeners → Edit → Binding
    - Haken bei *All Interfaces*
    - Datenbereich einschränken, um unnötige Kommunikation zu verhindern
  - Mobil-Gerät mit Proxy verbinden
    - Android: Einstellungen → WLAN → lange auf das Lab-Wi-Fi klicken, bis ein Auswahlfenster erscheint → Netzwerk ändern → Haken setzen bei Erweiterte Optionen → den Proxy auf Manuell setzen und Port sowie IP des Analyserechners eintragen.
    - iOS: Einstellungen → WLAN → auf das i in der Zeile des Lab-Wi-Fi klicken → den Proxy auf Manuell setzen und Port sowie IP des Analyserechners eintragen.
    - Windows 10 Mobile: Einstellungen → WLAN → Verwalten → Lab-Wi-Fi → den Schieber bei Proxy auf Ein schalten → Port sowie IP des Analyserechners eintragen
  - nicht vertrauenswürdiges Zertifikat muss erst akzeptiert werden
    - wenn das nicht passiert -> Verdächtig
- typische Orte: öffentliches WLAN

### 9.2.2 Man-in-the-Middle-Angriff mit vertrauenswürdigem Zertifikat
- Burp-Suite-Root-Zertifikat in unseren Trusted Certificate Store importieren 

1. Extrahieren des Root-Zertifikates aus der Burp Suite (Proxy → Options → Proxy Listeners → CA Certificate... → Export Certificate in DER format → Next → burp.cer), wie in Abbildung 9–4 gezeigt.
2. Importieren des Root-Zertifikates in das mobile Endgerät.
  - Android: `adb push burp.cer /sdcard/Downloads/` und auf dem Endgerät: Einstellungen → Sicherheit → Von Speicher installieren → Downloads → burp.cer auswählen und für Wi-Fi importieren 
  - iOS: Am einfachsten ist es, wenn wir uns das burp.cer-Zertifikat per E-Mail an das Testgerät senden. Dort können wir per Klick auf den E-Mail-Anhang das Zertifikat direkt installieren. Als Alternative können wir mittels Safari auf die URL http://burp/cert surfen und von dort das Zertifikat importieren.
  - Windows 10 Mobile: Am einfachsten ist es auch hier, wenn wir wie bei iOS beschrieben vorgehen.
3. Erneuter Verbindungs-Test –> keine Warnungen

- Gegentechnik: Certificate Pinning
  - bestimmte Punkte in der Vertrauenskette des Zertifikats fest verankert

### 9.2.3 Angriffe bei aktiviertem Certificate Pinning
- Root-Zugriff auf mobiles Gerät notwendig
  - bestimmte Software zu installieren
- Die App wird so verändert, dass alle Stellen, an denen das Zertifikat geprüft wird, ausgetauscht werden
- toolgestützt -> Zertifikatstests umgehen durch API-Hooking
  - Fehleranfällig
- Android
  - Tool: *Cydia Substrate*
  - Erweiterung: *Android-SSL TrustKiller*
    - liefert für alle Zertifikatstests TRUE zurück
  1. Installation der Android-SSL-TrustKiller-Applikation per `adb install Android-SSL-TrustKiller.apk`
  2. Öffnen der Substrate-Applikation
    a. Link Substrate Files
    b. Restart System (Soft)
  3. Starten der Test-Applikation -> Datenkommunikation mitschneiden
  - Probleme: *Android-SSL-Bypass*
- iOS 
  - *Snoop-it* wie *SSL-TrustKiller*
    - API-Aufrufen zur Zertifikatsüberprüfung -> TRUE
  - *iOS-SSL-Kill-Switch* -> Low-Level-SSL-Funktionen manipulieren
  - *TrustMe* 
    - deaktiviert die SecTrust-Evaluate-Methode -> alle Zertifikate gültig 


## 9.3 SSL-Strip-Angriffe
- SSL-Downgrade-Angriff
- Nutzung einer unverschlüsselte Verbindung
- Proxy *sslstrip*
- Ersetzen aller *https* durch *http*
- Vorgehen
  1. IP-Weiterleitung aktivieren
    - sudo sysctl -w net.inet.ip.forwarding=1
    - sudo sysctl -w net.inet.ip.fw.enable=1
    - sudo sysctl -w net.inet.ip.fw.verbose=1
  2. Paketfilter konfigurieren
    - Datei namens /etc/pfsslstrip.conf.
    - Inhalt: `rdr pass on en1 proto tcp from any to any port 80 -> 127.0.0.1 port 8080.`
    - Filter aktivieren `sudo pfctl -e -f pfsslstrip.conf`
  3. sslstrip starten `python sslstrip.py -l 8080 -w ./sslstrip.log`
  4. Proxy auf mobilen Endgerät setzen: PORT 8080, IP-Adresse anpassen
  5. Test-App starten und analysieren
- *sslstrip.log* -> Datenverkehr

## 9.4 Anwendungsbeispiele und Auswertung
### 9.4.1 Parameter in Paketen decodieren
- Daten als codierte Form
  - Base64 | URL-codiert
- Interessant: 
  - Typ POST -> Daten werden versendet
    - Parameter
    - `Authentication`-Header -> vlt. Nutzername + Passwort oder Token

### 9.4.2 Pakete abfangen und manipulieren
- mit Burp-Suite Interceptor
- Bsp
  - Timeout der S/MIME-PIN verändern -> PIN wird nicht mehr ungültig -> Freier Zugang an Daten
  - Fernlöschung deaktivieren -> mehr Zeit 
  - Jailbreak Erkennung deaktivieren
  - Löschbefehle blockieren











