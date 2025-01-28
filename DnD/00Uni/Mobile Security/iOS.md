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

