Aufgabe 1) Sicherheitsprinzipien: Nenne 3 Sicherheitsprinzipien

1. Marktplatz: Ein zentraler Ort, von dem die Anwendungen installiert werden
	1. Kontrolle des Herstellers
	2. Kontrolle auf Schwachstellen und Schadcode
2. Sandboxes: getrennte virutelle Maschienen in denen jeweils eine Anwendung läuft
	1. Trennung der Speicherbereiche verschiedener Apps (kein Datenauslesen von anderen Anwendungen)
	2. Einschränkung von Zugriffen auf das Betriebssystem, Komponenten des Mobil-Geräts
3. Rechteverteilung
	1. Apps dürfen Rechte anfragen, aber der Nutzer muss sie genhemigen
	2. Verhinderung, dass System-Komponenten genutzt werden, die für die Anwendung nicht notwendig sind
	3. Verhinderung unberechtigter Kommunikation

Aufgabe 2) Interprozesskommunikation (7 Punkte)
Wozu dienen Intents bei Android, wie sind diese aufgebaut und welche Art von Intents ist im
Hinblick auf die Applikationssicherheit als sicherer einzustufen? Begründen Sie Ihre Einschätzung.

- Intents werden für die Kommunikation zwischen Apps verwendet um Services zu nutzen und Daten auszutauschen
- Es ist ein eigenes Objekt mit folgenden Komponenten:
	- Action
	- Data
	- Component
	- Extra
	- Category
- Es gibt expliziete und implizite Intents
	- expliziete Intents sind sicherer, da diese Komponenten ansprechen, die exakt bekannt sind und meist nur innerhalb einer Anwendung liegen. Bei implizieten Intents können Daten von einer App an andere Apps abfließen, besonders wenn diese Intents durch unberechtigte Apps gelesen werden. Zudem weiß der Entwickler, der den implizieten Intent losschickt, nicht, welche App sich am Ende darum kümmert sondern hofft, dass es funktioniert. 

Aufgabe 3:
- iOS -> Secure Boot
	- Ablaufreihenfolge: Boot ROM -> LLB -> iBoot -> iOS Kernel
	- Die Boot ROM ist unveränderlich in Hardware vorliegend. Sie enthält den Public Key für die Apple Root-CA, womit sie die Signatur des Low Lewel Bootloader (LLB) vierifiziert
	- Der LLB startet viele Routinen von weiteren Programmen. Zudem verifiziert er die Signatur des iBoot im Flash. Wenn das Fehlschlägt, startet das iPhone im "Recovery Modus". Der iBoot verifiziert die Signatur des iOS-Kernels. 
	- Der iOS Kernel lädt das eigentliche Betriegssystem
- Android -> Verfified Boot
	- kontrolliert die Unveränderlichkeit der Gerätesoftware
	- "dm-verity" verifiziert den Boot-Prozess
	- Dabei werden neue Hashwerte aus der Software berechnet und mit gespeicherten verglichen

Aufgabe 4) Schutzlevel (4 Punkte)
Dateien und einzelne besonders schützenswerte Daten können unter iOS durch Nutzung spezieller Anwendungsprogrammierschnittstellen (API) geschützt werden. Diese bieten bestimmte Schutzlevel an.
- Welche dieser Schnittstellen wird für welchen Zweck eingesetzt? 
- Bei welchen Schutzlevels wird bei der Verschlüsselung zu speichernder Daten und Dateien auf die Verwendung von nutzerdefinierten Schlüsseln verzichtet

Die Keychain kann dafür genutzt werden. 
Verzichtet auf nutzerdefinierten Schlüssel wird bei kSecAttrAccessiblerAlways.
Es gibt auch NSFileProtection und da wird bei NSFileProtectionNone Schutz ohne PIN erstellt.

Aufgabe 5) Notwendigkeit der Applikationsanalyse (6 Punkte)
Weshalb ist es notwendig mobile Applikationen im Hinblick auf Sicherheitsaspekte selbst zu analysieren? Warum ist dies insbesondere bei Android Applikationen notwendig?

-   **Absichtliche Bedrohungen durch Malware und Spyware**
    
    -   Mobile Apps können absichtlich Malware, Spyware oder Grayware enthalten, die zu Datendiebstahl, böswilliger Geräteverschlüsselung oder Botnet-Aktivitäten (z. B. DDoS-Attacken) genutzt werden können.
-   **Unabsichtliche Sicherheitslücken**
    
    -   Applikationen können unbeabsichtigte Schwachstellen aufweisen, z. B. durch unverschlüsselte Speicherung sensibler Daten, die forensisch ausgelesen werden können. Das Vertrauen auf die Sandbox-Umgebung ist riskant, besonders bei manipulierten Geräten.
-   **Unsichere Datenflüsse und Logging**
    
    -   Daten können unbeabsichtigt durch fehlerhaftes Logging oder Seiteneffekte bei der Nutzung von Betriebssystemfunktionen abfließen, wodurch sensible Informationen offengelegt werden könnten.
-   **Installation aus unsicheren Quellen**
    
    -   Auf Android-Geräten können Apps aus Drittanbieter-Märkten oder nicht vertrauenswürdigen Quellen installiert werden, was das Risiko erhöht, Schadsoftware herunterzuladen.
-   **Fehlende Schutzmechanismen**
    
    -   Der Verzicht auf Schutzmaßnahmen wie Integritätsprüfungen, Code-Verschleierung (Obfuskation) oder die Nutzung von nativem Code (z. B. durch JNI) in sicherheitskritischen Abschnitten macht Apps besonders anfällig.
-   **Gefahr durch Reverse Engineering**
    
    -   Android-Apps können leicht einem Reverse Engineering unterzogen, mit Schadcode angereichert und unter gleichem oder ähnlichem Namen erneut verbreitet werden, was Nutzerdaten gefährden kann.


Aufgabe 6) Analysearten (4 Punkte)
Erläutern Sie die Ansätze der statischen und dynamischen Analyse und erklären Sie kurz, wie diese
sich ergänzen

- Statische Codeanalyse: Das Analysieren von Anwendungen, ohne das diese aktiv läuft. Dabei muss oft der Quellcode aus den kompilierten Dateien ermittelt werden. Sie gibt einem erste Ansätze und ein grundlegendes Verständnis der Anwendung
- Dynamsiche Codeanalyse: Das Analysieren von Anwendungen während diese Laufen. Dazu gehören die Überwachung von Netzkommunikation, ungewöhnliches Verhalten oder das Debuggen um besonderes Verhalten auszulösen. 
- Sie ergänzen sich hervorragend, da die statische Codeanalyse eine Grundlage gibt und die dynamsiche Analyse die Erwartungen bestätigen oder widerlegen kann. Zudem kann die statische Analyse Views oder Acitivities aufzeigen, auf welche man in der dynamsichen Analyse gar nicht gestoßen wäre. Somit hilft das eine, das andere besser durchzuführen und zu verstehen. 

Aufgabe 7) Aufbau von Laborumgebungen (9 Punkte)
In Kurseinheit 2 wurde ein mögliches Layout einer Laborumgebung beschrieben, die verschiedene
Hardwarekomponenten einsetzt. Zudem wird angenommen, dass für die Kommunikation mit ei-
ner Serverkomponente ein Backendserver in die Laborumgebung integriert wird (in Abb. 1-9 des
Basistextes nicht abgebildet). Eine analoge Laborumgebung soll unter Verzicht des Wi-Fi-Routers
(gerade bei forensischen Analysen ist Internetzugriff problematisch und zu vermeiden) aufgesetzt
werden. Dabei soll eine Basishardware (z.B. ein PC) alle für die Umgebung notwendigen Bestand-
teile über entsprechende Software implementieren. Aufgrund des Verzichts auf einen Wi-Fi-Router
soll auch die Komponente des Backendservers über eine Softwarelösung simuliert werden.
Hinweis: Bei der Aufgabenstellung soll nur die Implementierung von Hardware-Komponenten über
entsprechende Software-Lösungen betrachtet werden. Auf eine Beschreibung der zu implementie-
renden Analyseprogramme soll verzichtet werden.
a) Welche Systemkomponenten müssen in einem System, wie das oben beschriebene, implemen-
tiert werden? Welche Software-Lösungen können die Hardwarekomponenten ersetzen? Geben
Sie mind. je ein konkretes Beispiel für eine kostenlose Software an, die Sie bei Ihren Recher-
chen gefunden haben. (6 Punkte)
b) Angenommen Sie möchten eine Peer-to-Peer Applikation (diese Art Applikation hat eine
Client- und eine Server-Komponente implementiert) analysieren. Dazu müssen Sie den Netz-
werkverkehr zwischen zwei Peer-Instanzen genauer betrachten. Wie ist diese Lösung anzu-
passen, wenn die Kommunikation vom lokalen Proxy abgefangen werden soll? (1 Punkt)
c) Es wird angenommen als Emulator-Instanz wird ein offizieller Android Emulator (z.B. über
Android Studio erstellbar) verwendet. Wird für die Kommunikation in b) eine zusätzliche
Router-Software auf dem Analyserechner benötigt? Begründen Sie kurz Ihre Antwort. (2
Punkte

a) 
|Hardware|Lokale Software|Beispiele|
|-|-|-|
|Mobile Device|Emulator|Offizieller Android Emulator|
|MitM-fähiger Proxy|MitM-fähige Proxy-Software|Burpsuite|
|Backend-Server|(Anwendungs-/Http(s))Server-Software|Apache Tomcat|

b) zwei Emulatoren von Geräten und jeweils den Proxy auf den Geräten einstellen. Mit dem Proxy den Datenverkehr überwachen

c) nein, da sie bereits so konfiguriert sind, dass sie auf den Proxy zugreifen und der Proxy leitet sie dann entsprechend weiter. 

Aufgabe 1) Pipeline von Analysewerkzeugen (9 Punkte)
Sie stehen vor der Aufgabe, eine einfache Webapplikation zu erstellen, die über eine (dynamische) Webseite Nutzern die statische Analyse von hochladbaren Android-Applikationen (Dateiendung .apk) ermöglicht. Der Quellcode der jeweils enthaltenen Java-Klassen sowie das jeweilige Android-Manifest sollen im Klartext (strukturierte Ausgabe ist erlaubt) nach der Analyse als Ausgabe angezeigt werden. Hierzu sollen notwendige, passende und kostenlose Analysewerkzeuge im Backend-Server der Webapplikation zum Einsatz kommen. 

Hierbei sollen die Komponenten zur 
- Extraktion des Android-Manifests 
- Konvertierung von Dalvik-Bytecode in Java-Bytecode 
- redundant 
- zwei parallel ausführbare Pipelines
- 
Hierbei erhebt sich die zu beantwortende Frage: 
Welche Werkzeuge werden zu welchem Zweck wie miteinander kombiniert?

2)
a) In welcher Hinsicht geben aufgeführte Berechtigungen einen Hinweis auf petenziell gefähliches Verhalten der App?
- Wenn man weiß, was die App machen soll und sie Berechtigungen anfragt, die sie nicht braucht, ist das verdächtig
- Wenn sie sensible Berechtigungen anfragt, kann das ein Hinweis sein
b)
- nur dann, wenn man sie braucht
- nur soviele man benötigt



3)
a) aus welchen Kernkomponenten besteht Drozer?
- Agent
- Backend-Server
- console

b) Debugging, API-Hooking, dynamsiche Analyse
c) 
- Verändern von Variablen, zB. die Anzahl versuchter Passworteingaben zurück auf 0 setzen
- Aufzeichnen von Zugriffen auf Dateien
- 
