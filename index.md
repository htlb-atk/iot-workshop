<!-- TOC depthFrom:1 depthTo:3 withLinks:1 updateOnSave:1 orderedList:0 -->

- [IoT Workshop](#iot-workshop)
	- [Komponenten einer IoT Anwendung](#komponenten-einer-iot-anwendung)
	- [Übersicht Datenfluss und Kommunikation](#übersicht-datenfluss-und-kommunikation)
- [Das „Ding“: LoPy](#das-ding-lopy)
	- [LoPy Developement Boards](#lopy-developement-boards)
	- [Expansion Board](#expansion-board)
	- [Atom Editor mit pymakr plug-in](#atom-editor-mit-pymakr-plug-in)
- [Temperaturmessung](#temperaturmessung)
	- [Adafruit Interface Board](#adafruit-interface-board)
		- [Verdrahtung LoPy - MAX31865 Interface Board](#verdrahtung-lopy-max31865-interface-board)
		- [Verdrahtung mit Steckplatine](#verdrahtung-mit-steckplatine)
	- [SPI-Bus Interface](#spi-bus-interface)
		- [Eigenschaften](#eigenschaften)
		- [LoPy SPI-Bus](#lopy-spi-bus)
		- [Klasse lopy\_max31865](#klasse-lopymax31865)
		- [Anwendung der Klasse lopy\_max31865](#anwendung-der-klasse-lopymax31865)
	- [Temperaturwerte auslesen](#temperaturwerte-auslesen)
		- [Bibliothek „lopy\_max31865“ auf den LoPy übertragen.](#bibliothek-lopymax31865-auf-den-lopy-übertragen)
		- [REPL](#repl)
		- [Python Programm](#python-programm)
- [ThingSpeak](#thingspeak)
	- [ThingSpeak Konto einrichten](#thingspeak-konto-einrichten)
	- [ThingSpeak Channel](#thingspeak-channel)
		- [Neuen Channel erstellen](#neuen-channel-erstellen)
	- [ThingSpeak API](#thingspeak-api)
		- [REST API](#rest-api)
		- [MQTT API](#mqtt-api)
- [LoPy – ThingSpeak](#lopy-thingspeak)
	- [WLAN Verbindung](#wlan-verbindung)
	- [MQTT Client](#mqtt-client)
- [Auswertung der Daten mit MATLAB und IFTTT](#auswertung-der-daten-mit-matlab-und-ifttt)
	- [Neuer Channel für die Auswertung](#neuer-channel-für-die-auswertung)
		- [Neuen Channel *motor\_1\_calc* anlegen](#neuen-channel-motor1calc-anlegen)
		- [MATLAB Analysis](#matlab-analysis)
		- [Reaktion auf neuen Temperaturwert](#reaktion-auf-neuen-temperaturwert)
		- [Reaktion auf große Temperaturänderung](#reaktion-auf-groe-temperaturänderung)
- [IFTTT (If This Than That)](#ifttt-if-this-than-that)
	- [IFTTT Konto anlegen](#ifttt-konto-anlegen)
	- [IFTTT Applet erstellen](#ifttt-applet-erstellen)
	- [IFTTT mit ThingSpeak verbinden](#ifttt-mit-thingspeak-verbinden)
		- [Webhooks Dokumentation](#webhooks-dokumentation)
		- [ThingHTTP Request erstellen](#thinghttp-request-erstellen)
		- [React erstellen](#react-erstellen)
- [Zusammenfassung](#zusammenfassung)

<!-- /TOC -->
# IoT Workshop
In diesem Workshop erstellen wir gemeinsam eine einfache IoT Anwendung.

Wir messen mit einem Sensor die Temperatur einer Maschine, verbinden den Sensor über SPI -Bus mit einem IoT-Controller (LoPy) und übertragen die Messwerte zur ThingSpeak Plattform in die Cloud. Dort stellen wir die Messwerte graphisch dar und analysieren sie. Wenn der Temperaturgradient zu groß ist, wird eine E-Mail Nachricht auf das Handy gesendet.

## Komponenten einer IoT Anwendung

![](./media/image2.png)

Mindmap IoT als PDF-Datei: [<span class="underline">IoT.pdf</span>](./media/IoT.pdf)

Aus den vielen Möglichkeiten wählen wir für diesen Workshop von jeder Kategorie eine aus.

Als Thing verwenden wir einen LoPy-Mikrocontroller, der über ein SPI-Interface einen PT100 Temperatursensor ausliest. Den Netzwerkzugang realisieren wir über WLAN. Die ThingSpeak Plattform stellt uns den Cloud Service bereit. Die Temperaturwerte analysieren wir mit Hilfe von MATLAB. Die Visualisierung erfolgt auf einem Dashboard. Mit IFTTT (If This Than That) realisieren wir eine Reaktion auf die Auswertung.

![](./media/image3.png)

Mindmap als PDF-Datei: [<span class="underline">IoT\_Workshop.pdf</span>](./media/IoT_Fasttrack.pdf)

Der Workshop gliedert sich in folgende Teilaufgaben:

  - LoPy und Temperatursensor auf Steckbrett verdrahten
  - Temperaturwerte „manuell“ auslesen
  - ThingSpeak Channel erstellen
  - ThingSpeak API austesten
  - LoPy mit WLAN verbinden
  - Temperaturwerte an ThingSpeak senden
  - Temperaturverlauf mit MATLAB auswerten
  - Mit IFTTT auf Temperaturänderung reagieren

## Übersicht Datenfluss und Kommunikation
Das folgende Diagramm zeigt das Zusammenspiel der einzelnen Komponenten und ist die **wichtigste Abbildung** in diesem Workshop. Das Diagramm liefert den roten Faden. Wenn der Überblick verloren geht, bitte an diesem Diagramm neu orientieren. Es muss zu jedem Zeitpunkt klar sein, an welchem "Kästchen" wir arbeiten.
![](./media/IoT_Datenfluss_gesamt.png)

# Das „Ding“: LoPy

## LoPy Developement Boards

Der LoPy ist ein Mikrocontroller Board auf Basis des Espressif ESP32 chipset. Er unterstützt die drahtlosen Technologien WLAN, Bluetooth Low Energy (BLE) und LoRa. Andere Vertreter dieser Produktfamilie unterstützen weitere Kommunikationsstandards wie z. B. Sigfox oder LTE-M.  
Der Stromverbrauch im Deep-sleep beträgt nur ca. 25µA.

Ein eingebauter MicroPython Interpreter ermöglicht eine einfache
Programmierung.

![](./media/image5.png)

Für Eigenentwicklungen sind auch OEM Boards erhältlich.

![](./media/image6.png)

## Expansion Board

![](./media/image7.png)   
Die Hard- und Softwareentwicklung wird durch das Expansion Board erleichtert:

  - LoPy Anschlüsse auf Pfosten geführt,
  - USB-Anschluss für Anschluss an einen PC,
  - LiPo Ladegerät
  - eine LED und ein Taster
  - MicroSD-Karten Slot

![](./media/image8.png)

## Atom Editor mit pymakr plug-in

Der LoPy hat bereits einen MicroPython Interpreter eingebaut, es wird daher keine Compiler-Umgebung benötigt. Für die Programmierung verwenden wir den Atom-Editor mit einem speziellen Plug-In für die Kommunikation mit dem LoPy.

> Aktivität: Verbindung zum LoPy herstellen.

  1. Atom Editor starten ![](./media/image9.png)
  2. Für die Screenshots wurde das Thema von „Atom Dark“ auf „Atom Light“ umgestellt.  
    File --> Settings --> Themes
    ![](./media/image10.png)
  3. Der LoPy wird an eine USB-Schnittstelle angeschlossen und von dort auch mit Energie versorgt. Auf dem PC erscheint die Verbindung als zusätzliche COM-Schnittstelle.
  4. \>\>\> zeigt eine erfolgreiche Verbindung mit dem LoPy an. Am oberen Rand des Fensters wird „Connected“ angezeigt. Einige Male die Enter-Taste drücken: Jedesmal muss ein neuer Prompt (\>\>\>)angezeigt werden.   
    Failed to connect:  
	 Standardmäßig wird automatisch die höchste USB-Schnittstelle für die Kommunikation mit dem Board verwendet.
	 ![](./media/Atom_Settings_Autoconnect.png)
    Eventuell muss zuerst noch die richtige Schnittstelle manuell eingestellt werden.  
	 Autoconnect on USB deaktivieren.
    Mit More --\> Get Serial Ports werden alle erkannten Schnittstellen angezeigt.  
    ![](./media/image11.png)
    Unter Settings Global Settings Device Address wird die gewünschte COM eingetragen.
    ![](./media/image12.png)

# Temperaturmessung

## Adafruit Interface Board

Die integrierte Schaltung MAX31865.pdf wandelt ein Widerstandsverhältnis in einen 15bit Digitalwert. Die Schaltung ist besonders für die Auswertung von PT100 und PT1000 Elementen geeignet. Die Kommunikation mit dem LoPy erfolgt über das SPI-Bus Interface.  
[<span class="underline">Datenblatt MAX31865</span>](./media/datenblatt_max31865.pdf).  
Wir verwenden das Interface Board "Adafruit PT100 RTD Temperature Sensor Amplifier - MAX31865". Das Board ist unter anderem mit einem MAX31865 und einem Referenzwiderstand mit 430Ω bestückt.      
![Adafruit MAX31865 Interface Board](./media/image13.png)
Datenblatt: [<span class="underline">adafruit-max31865-rtd-pt100-amplifier.pdf</span>](.\media\adafruit-max31865-rtd-pt100-amplifier.pdf)
### Verdrahtung LoPy - MAX31865 Interface Board

![Verdrahtung LoPy](./media/Verdrahtung_LoPy_MAX31865_P12-P14.png)

Verdrahtungsplan als PDF-Datei: [<span class="underline">Verdrahtungsplan</span>](./media/Verdrahtung_LoPy_MAX31865_P12-P14.pdf)
> Aktivität: LoPY mit MAX31865 Board verdrahten.

### Verdrahtung mit Steckplatine

![Verdrahtung-Steckplatine](./media/image15.png)
__Achtung:__ Die Position der weißen Leitung ist auf der Abbildung veraltet! --> siehe Verdrahtungsplan.

## SPI-Bus Interface

### Eigenschaften

siehe auch [**Wikipedia: SPI-Bus**](https://de.wikipedia.org/wiki/Serial_Peripheral_Interface)

  - Drei gemeinsame Leitungen, an denen jeder Teilnehmer angeschlossen ist.
      - SCLK (Serial Clock) auch SCK, wird vom Master zur Synchronisation ausgegeben
      - MOSI (Master Output, Slave Input) oder SIMO (Slave Input, Master Output)
      - MISO (Master Input, Slave Output) oder SOMI (Slave Output, Master Input)
  - Die Datenleitungen werden manchmal auch
      - SDO (Serial Data Out) und
      - SDI (Serial Data In) genannt.
    Hier muss MISO mit SDO und MOSI mit SDI verbunden werden.
  - Eine oder mehrere mit logisch-''0'' aktive Chip-Select-Leitungen,
    welche alle vom Master gesteuert werden, und von denen je eine
    Leitung pro Slave vorgesehen ist. Diese Leitungen werden
    beispielsweise mit C̅S̅ (Chip Select) bezeichnet.
  - Vollduplexfähig 
  - Viele Einstellmöglichkeiten, wie
      - mit welcher Taktflanke ausgegeben oder eingelesen wird
      - Wortlänge
      - Übertragung: MSB oder LSB zuerst 
  - Unterschiedliche Taktfrequenzen bis in den MHz-Bereich sind zulässig.
  - Vier unterschiedliche Modi CPOL=polarity, CPHA=phase  
    ![](./media/image16.png)

Viele Einstellungsmöglichkeiten sind unter anderem deshalb erforderlich, weil die Spezifikation für den SPI-Bus in vielen Eigenschaften nicht festgelegt ist, wodurch verschiedene, zueinander inkompatible Geräte existieren. Häufig ist beispielsweise für jedes angeschlossene IC eine eigene Konfiguration des steuernden Mikrocontrollers (Master des SPI-Bus) erforderlich.
### LoPy SPI-Bus
Der LoPy besitzt eine Hardware-SPI-Schnittstelle. Die Klasse "SPI" stellt Funktionen für die Buskommunikation zur Verfügung.  
Der Link [<span class="underline">https://docs.pycom.io/firmwareapi/pycom/machine/spi.html</span>](https://docs.pycom.io/firmwareapi/pycom/machine/spi.html)
führt zur detaillierten Dokumentation.

Die gesamte Kommunikation mit dem MAX31865 ist in einer Klasse
(Bibliothek) abgelegt und kann in das eigene Programm importiert werden.

### Klasse lopy\_max31865

Die Klasse „lopy\_max31865.py“ wickelt die Kommunikation mit dem MAX31865 über den SPI Bus ab, und stellt dem Anwenderprogramm Funktionen zum Auslesen der Temperatur bereit. Zu diesem Zweck verwendet sie nur
drei SPI-Funktionen:

``` python
   # SPI-Schnittstelle initialisieren:  
   spi = SPI(0, mode=SPI.MASTER, baudrate=100000, polarity=0, phase=1, firstbit=SPI.MSB)

   # Inhalt von buf über den SPI-Bus ausgeben:  
   spi.write(buf)

   # n-Bytes von der SPI-Schnittstelle einlesen  
   buf=spi.read(n)
```

Der MicroPython Programmcode für die Klasse „lopy\_max31865“ steht unter:  
[<span class="underline">https://github.com/htlb-atk/iot-workshop/blob/master/lib/lopy\_max31865.py</span>](https://github.com/htlb-atk/schilf-iot-MAX31865/blob/master/lib/lopy_max31865.py)

Für andere Bausteine mit SPI-Schnittstelle kann diese Klasse als Ausgangbasis dienen.

### Anwendung der Klasse lopy\_max31865

Die Klasse „lopy\_max31865.py“ stellt die Verbindung zum Interface-Board her. Dort wird die ganze Kommunikation mit dem IC MAX31865 abgewickelt. Auch die Umrechnung der 15bit-Zahl in eine Temperatur in °C erfolgt in dieser Klasse. Der Anwender kann mit der Methode „read()“ die Temperatur auslesen.

Bevor wir das ausprobieren können, müssen wir uns aber noch die Programmbibliothek von GitHub holen und auf den LoPy übertragen.

```python
   # Die Klasse lopy_max31865 in das eigene Programm importieren  
   from lopy_max31865 import MAX31865

   # Ein neues Objekt mit dem Namen rtd anlegen  
   rtd = MAX31865()

   # Die Temperatur in °C auslesen  
   temp = rtd.read()
```


## Temperaturwerte auslesen

### Bibliothek „lopy\_max31865“ auf den LoPy übertragen.

Die Bibliothek lopy\_max3185 ist auf GitHub öffentlich zugänglich. GitHub ist eine Plattform für gemeinsame Softwareentwicklung. Viele Open Source Projekte werden auf GitHub entwickelt. Auch LoPy, Arduino und Raspberry Bibliotheken werden meistens auf GitHub entwickelt und können von dort kopiert werden.

In Atom ist ein GitHub Client integriert. Es können ganz einfach ganze Repositories (ganze Verzeichnisstrukturen mit Zusatzinfos) auf den PC heruntergeladen (clone) werden.

#### Clone Repository von GitHub

  1. Das Repository [<span class="underline">https://github.com/htlb-atk/iot-workshop</span>](https://github.com/htlb-atk/iot-workshop/) auswählen
  2. Download-Link in die Zwischenablage kopieren
     ![](./media/image19a.png)
     ![](./media/image20a.png)
	  Falls "Copy to Clipboard" vom Browser verhindert wird, muss die URL markiert und mit Ctrl-c in die Zwischenablage kopiert werden.
  3. Zurück zum Atom Editor, mit Strg-Shift-P (macOS: Cmd-Shift-P) die Command Palette öffnen und "GitHub: Clone wählen" .
     ![](./media/image21.png)
  4. Mit Strg-v den Pfad aus der Zwischenablage in das Feld „Clone from“ einsetzen.
     ![](./media/image22.png)

Damit wird das komplette Repository in das Verzeichnis Z:\\github kopiert und als Projekt in Atom geöffnet. GitHub bietet viele Möglichkeiten zur gemeinsamen Codeentwicklung. In diesem Workshop werden wir jedoch keine weiteren Funktionalitäten von GitHub verwenden.

#### Ordnerstruktur mit Bibliothek auf LoPy übertragen
Sync oder Upload überträgt das Projekt an den LoPy.
![](./media/image23.png)

### REPL

Sobald der LoPy mit Atom verbunden ist, befindet er sich in einem interaktiven MicroPython Modus, der sogenannten Read-Evaluate-Print-Loop (REPL). Wir können Python Befehle eingeben, diese werden sofort ausgeführt und das Ergebnis des Befehls wird angezeigt. Damit können wir nun die Temperatur des PT100 Sensors auslesen und anzeigen lassen.
![](./media/image24.png)

### Python Programm
Wir schreiben nun diese Befehle in die Datei main.py und packen sie in eine Schleife. main.py wird bei jedem Neustart des LoPy automatisch ausgeführt.

1. Neue Datei main.py anlegen
   ![](./media/image25.png)
   ![](./media/image26.png)
2. Programm in main.py eingeben
   ``` python
   # Anzeige PT100 Temperatur
   import time
   from lopy_max31865 import MAX31865

   rtd = MAX31865()
   while True:
      temp = rtd.read()
      print("Temperatur: ", temp)
      time.sleep(5)
   ```

    *WICHTIG:*  
    In Python werden alle Zeilen, die zu einer while-Schleife gehören, gleich weit eingerückt. Leerzeichen am Zeilenanfang dürfen **nicht** beliebig eingefügt werden\! Dies ist ein wesentlicher Unterschied zu anderen Programmiersprachen. Besonders bei verschachtelten Schleifen, If-Abfragen und kopierten Codeblöcken muss auf die Einrückung geachtet werden.
  3. Programm __abspeichern__\! Sync erkennt sonst keine Änderung und überträgt nichts.
  4. Sync / Upload
    ![](./media/image28.png)

Das Programm wird mit Sync an den LoPy übertragen und sofort ausgeführt.
**Unser erstes MicroPython Programm läuft.**
Im nächsten Schritt müssen wir uns die IoT Plattform ThingSpeak einrichten, damit wir anschließend die Temperaturwerte dorthin übertragen können.

# ThingSpeak
In diesem Abschnitt werden wir Messwerte an ThingSpeak übertragen. Die folgende Abbildung zeigt noch einmal den Datenfluss zu ThingSpeak.
![](./media/IoT_Datenfluss_1.png)
## ThingSpeak Konto einrichten

Für diesen Workshop verwenden wir ein Gratiskonto von ThingSpeak. ThingSpeak ist die IoT-Plattform der Firma MathWorks, die auch die MATLAB/Simulink Produkte anbietet. MATLAB/Simulink ist an der HTL-Bregenz für alle Lehrer und Schüler lizensiert. Damit uns für die Auswertungen alle MATLAB-Funktionen zur Verfügung stehen, müssen wir uns mit einer **Schul-E-Mail-Adresse** registrieren.

1. [<span class="underline">https://thingspeak.com</span>](https://thingspeak.com)
   ![Thingspeak-01](./media/image29.png)

2. Unbedingt mit der **Schul-E-Mail-Adresse** registrieren. ![Thingspeak Sign Up](./media/image30.png)
Das Feld "UserID" muss unbedingt ausgefüllt werden!
3. An die E-Mail-Adresse wird eine Bestätigungs-Link gesendet (dauert ein paar Sekunden). E-Mail öffnen und "Verify your email" klicken. ![Tingspeak Verify E-Mail](./media/image31.png)
4. ThingSpeak Webseite öffnen [<span class="underline">https://thingspeak.com</span>](https://thingspeak.com)
   ![ThingSpeak Sign In](./media/image32.png)
5. Schul-E-Mail-Adresse eingeben
   ![ThingSpeak Sign In 2](./media/image33.png)
6. Das **vorher gewählte** Passwort angeben (Passwort für MathWorks Account) und einloggen.  
   ![ThingSpeak Log In](./media/image34.png)
7. Meldung über die erfolgreiche Registrierung bestätigen.
   ![ThingSpeak Sign In Success](./media/image35.png)
8. Den Nutzungsbedingungen zustimmen.
   ![ThingSpeak Terms of use](./media/image36.png)
9. Damit ist die Registrierung abgeschlossen und ThingSpeak kann verwendet werden.
   ![ThingSpeak Main Page](./media/image37.png)

Das Konto ist nun angelegt und wir können eigene **Channels** anlegen.

##  ThingSpeak Channel

Wir möchten unsere Temperaturwerte abspeichern, grafisch darstellen und
auswerten. Dazu müssen die Werte mit einem Zeitstempel versehen und
abgespeichert werden.

In ThingSpeak werden die Werte in sogenannten "Channels" organisiert. Man kann sich einen Channel wie eine Tabelle mit (fast) beliebig vielen Zeilen und maximal acht Datenspalten (Feldern) vorstellen. Immer wenn über das Internet neue Werte an den Channel gesendet werden, wird eine neue Zeile erstellt. Zusätzlich zu den Datenspalten wird noch ein Zeitstempel (created\_at) generiert und eine laufende Nummer (entry\_id) vergeben.

Beispiel für eine Tabelle mit einem Datenfeld:   
![channeldata](./media/image38.png)

### Neuen Channel erstellen
In unserem Beispiel sollen die gemessenen Temperaturwerte in einem Channel erfasst werden. Wir erstellen dafür einen Channel "motor\_1" mit einem Datenfeld für die Umgebungstemperatur "Tu".

1. Anmelden auf [<span class="underline">https://thingspeak.com</span>](https://thingspeak.com) und "Channels --\> My Channels" auswählen
   ![ ThingSpeakTM Channels Y My Channels New Channel Apps Community](./media/image39.png)

2. Channel Name: **motor\_1**, Field1: **Tu**
   ![ ThingSpeak New Channel](./media/image40.png)
3. Nach unten scrollen und "Save Channel"
   ![ ThingSpeak Save Channel](./media/image41.png)

4. Damit steht ein neuer Channel "motor\_1" bereit. Ein leeres Diagramm wird automatisch erstellt und kann später angepasst werden. Wichtig ist die Channel ID, sie wird später noch benötigt.
   ![Channel ID: 381619](./media/image42.png)

## ThingSpeak API

Wie kommen nun die Messwerte in den Channel? Dafür gibt es ein Application Programming Interface, kurz API genannt. ThingSpeak bietet zwei unterschiedliche APIs an:

- REST API
- MQTT API

### REST API

REpresentational State Transfer (REST) ist eine Programmierschnittstelle, die HTTP oder HTTPS Anfragen verwendet, um per GET, PUT, POST und DELETE mit einem Server zu kommunizieren.

Der Zweck von REST liegt hauptsächlich in der Maschine-zu-Maschine-Kommunikation.

Was genau an den Server gesendet werden muss, um einen gewünschten Effekt zu erzielen, steht in der API Dokumentation des jeweiligen Dienstes.

In diesem Workshop möchten wir neue Temperaturwerte an einen Channel senden. Den dafür notwendigen HTTP-Aufruf finden wir in der [<span class="underline">REST API Dokumentation</span>](https://de.mathworks.com/help/thingspeak/rest-api.html) von ThingSpeak unter [<span class="underline">Update a Channel Feed</span>](https://de.mathworks.com/help/thingspeak/update-channel-feed.html).

In dieser Dokumentation stehen zwar alle Details, der notwendige HTTP(S)-Aufruf muss aber selbst zusammengesetzt werden. Wir wählen einen einfacheren Weg, müssen uns vorher aber noch mit API Keys beschäftigen.

#### API Keys

Der Client, in unserem Fall der LoPy, muss sich gegenüber ThingSpeak authentifizieren, sonst könnte jeder unsere Daten verändern. Dafür werden aber nicht ein Username und ein Passwort verwendet, sondern ein sogenannter API Key. Dieses Verfahren wird bei sehr vielen APIs von Webdiensten eingesetzt.

ThingSpeak erstellt für jeden Channel automatisch zwei Schlüssel: Einen **Write API Key** und eine **Read API Key**.

1. Die Schlüssel finden wir unter Channels --\> My Channels
   ![ThingSpeak MyChannel Motor_1](./media/image43.png)
2. Channel *motor\_1* auswählen; *API Keys* auswählen
   ![Channel ID: 381619 Messwerte Motor](./media/image44.png)
   ![Requests Update a ChannelFeed](./media/image45.png)

##### Write API Key

Dieser Schlüssel muss mitgesendet werden, wenn neue Daten in den Channel
geschrieben werden.

##### Read API Key

Dieser Schlüssel muss mitgesendet werden, wenn Daten aus einem Channel gelesen werden. Es können mehrere Keys generiert und an unterschiedliche Nutzer weitergegeben werden.
Für Channels, die auf Public gesetzt werden und daher öffentlich zugänglich sind, wird dieser Schlüssel nicht benötigt.

#### API Requests

Auf der Seite mit den API Keys werden auch Beispiele für HTTP-Aufrufe angezeigt

##### API Request mit Webbrowser - Werte schreiben

Wir können das Feld "Update a Channel Feed" kopieren, anpassen und im Browser (z.B. Internet Explorer) ausprobieren.

1. Feldinhalt "Update a Channel Feed" kopieren
   `GET https://api.thingspeak.com/update?api_key=UPTTFJC3VVxxxxxx&field1=0`
2. Das GET muss entfernt werden, der Webbrowser sendet standardmäßig einen GET Request:
   `https://api.thingspeak.com/update?api_key=UPTTFJC3VVxxxxxx&field1=0`
3. Der Temperaturwert steht in unserem Channel *motor\_1* im Feld 1 (Tu). Wir senden den Wert 19.9 an den Channel. Im Parameter api\_key muss der "Write API Key" mitgesendet werden, der für jeden Channel einen eigenen Wert besitzt.
   `https://api.thingspeak.com/update?api_key=UPTTFJC3VVxxxxxx&field1=19.9`
4. In unserem Channel sehen wir nun den ersten Datenpunkt.
   [https://thingspeak.com/channels/**381619**/private_show](https://thingspeak.com/channels/381619/private_show)   
   Channel ID **381619** durch die **eigene Channel ID** ersetzen\!   
   ![show channel](./media/image46.png)

**Achtung:** Mit einem kostenlosen ThingSpeak Konto kann höchstens alle **15s** ein Update an den Channel gesendet werden. Bezahlte Konten dürfen einmal pro Sekunde updaten.

> Jedes Gerät/Programm, das eine Webseite aufrufen kann, kann auch Daten in einen ThingSpeak Channel schreiben\!
   > [<span class="underline">https://api.thingspeak.com/update?api\_key=XXXXXXXXXX\&field1=19.9</span>](https://api.thingspeak.com/update?api_key=XXXXXXXXXX&field1=19.9)

##### API Request mit Webbrowser - Werte lesen

Mit einem einfachen Aufruf können wir die Daten aus einem Channel auslesen. Einfach folgende Zeile in den Webbrowser eingeben. Für das Lesen wird ein *Read API Key* des Channnels eingesetzt.
[<span class="underline">https://api.thingspeak.com/channels/381619/fields/1.csv?api\_key=TZU1G2WP033AGCEJ\&results=2</span>](https://api.thingspeak.com/channels/381619/fields/1.csv?api_key=TZU1G2WP03xxxxxx&results=2)

Eine genaue Beschreibung der Parameter steht in der ThingSpeak Dokumentation
[<span class="underline">https://de.mathworks.com/help/thingspeak/get-channel-field-feed.html</span>](https://de.mathworks.com/help/thingspeak/get-channel-field-feed.html)

#### Postman

API Aufrufe mit vielen Parametern können sehr komplex werden. POST, PUT und DELETE Requests können nicht einfach mit einem Webbrowser gesendet werden.

Für das Austesten von REST API Aufrufen ist das Programm **Postman** sehr hilfreich.
![Postman](./media/image47.png)

Über eine grafische Oberfläche können API Aufrufe erstellt, abgespeichert und gesendet werden. Antworten des Servers werden im unteren Bereich angezeigt. Mit Postman sieht man sehr genau, was tatsächlich an den Server (Parameter, Header, Body, ...) gesendet wird.

Aufrufe können in Postman erstellt und getestet werden. Wenn der Aufruf in Postman funktioniert, kann er in das eigene Programm (z.B. auf dem LoPy) eingebaut werden.
Auch für die Fehlersuche ist das Programm Postman sehr empfehlenswert.

#### Hookbin

Für die Fehlersuche ist es oft nützlich zu sehen, was beim Server überhaupt ankommt. Diese Frage kann _hookbin_ beantworten.
[<span class="underline">https://hookbin.com</span>](https://hookbin.com)

![Inspect HTTP Requests](./media/hookbin.png)

„CREATE NEW ENDPOINT“ liefert eine URL zurück, an die beliebige http-Aufrufe gesendet werden können.

![RequestBin URL](./media/hookbin_endpoint.png)

**Beispiel:** Der Aufruf   
[<span class="underline">https://hookb.in/Z23D0eNWbkC7q7eYQmqa?api\_key=UPTTFJC3VV33QGZ\&field1=19.0</span>](https://hookb.in/Z23D0eNWbkC7q7eYQmqa?api_key=UPTTFJC3VV33QGZ&field1=19.0)   
liefert

![HTTP Request Details](./media/hookbin_request.png)

Die vom Server empfangenen Parameter und Header werden detailliert aufgelistet.

### MQTT API

Bei der vorher beschriebenen REST API gibt es keinen Rückkanal. Wir
können den Channel auf Änderungen abfragen, der Channel kann aber nicht
von sich aus eine Nachricht senden. MQTT ist ein Standardprotokoll für
IoT-Anwendungen mit einigen bemerkenswerten Eigenschaften.

#### MQTT Eigenschaften

Eine gute Einführung in das Thema MQTT liefert
[<span class="underline">https://www.hivemq.com/mqtt-essentials/</span>](https://www.hivemq.com/mqtt-essentials/)

##### Publish/Subscribe Prinzip

MQTT funktioniert nach dem Publish/Subscribe-Verfahren. Die Datenproduzenten und -nutzer verbinden sich zu einem zentralen Server (MQTT-Broker). Das Senden (publish) und Empfangen (subscribe) von Nachrichten funktioniert über sogenannte Topics.

Eine mobile App kann sich nun ebenfalls zum Broker verbinden und den gleichen Topic abonnieren (subscribe), um alle Nachrichten zu erhalten, die der Temperatursensor versendet. Die komplette Kommunikation funktioniert rein über Topics, und der Sensor und das Mobiltelefon wissen nichts über die Existenz des jeweils anderen.
![QTT Publish_Subscribe](./media/image51.png)

##### Mechanismen zur Qualitätskontrolle

Ein weiteres wichtiges Konzept sind die drei **Servicequalitäten (QoS)** bei der Datenübertragung 0, 1 und 2. Dazu muss man wissen, dass MQTT auf TCP basiert, weshalb eine große Zuverlässigkeit bei der Übertragung gegeben ist. Trotzdem ist das bei Funknetzen keine ausreichende Lösung. Daher hat das Protokoll Mechanismen eingebaut, die das erfolgreiche Übertragen von Nachrichten garantieren.

Die Zusicherung variiert von **keiner Garantie** (Level 0) über die, dass die Nachricht **mindestens einmal** ankommt (Level 1), bis hin zur Garantie, dass die Nachricht **genau einmal** ankommt (Level 2). Der Unterschied zwischen Level 1 und 2 liegt darin, dass es bei Level 1 passieren kann, dass eine Nachricht öfter einen Client erreicht. Je nach Anwendungsfall sollte der passende Level gewählt werden, denn je höher der Level, desto höher ist die benötigte Bandbreite.
Nicht jeder Broker unterstützt alle Servicequalitäten!

##### Letzter Wille und gespeicherte Nachrichten

Jeder Client kann beim Verbinden eine Nachricht mit seinem letzten Willen (last will and testament) an den Broker schicken. Sobald der Broker das Unterbrechen der Verbindung merkt, sendet dieser die Nachricht im Auftrag des Clients an alle Subscriber.

Neu verbundene Clients (subscriber) müssen eventuell sehr lange auf den ersten Wert warten. Wenn z.B. ein Temperatursensor seinen Wert alle 15 Minuten published, muss ein Client, der die Temperatur abonniert, im schlimmsten Fall 15 Minuten auf den ersten Wert warten. Abhilfe schafft hier eine "**Retained Message**". Sie ist eine Nachricht, die vom Broker gespeichert und jedem Client, der sich neu verbindet und den jeweiligen Topic abonniert, sofort zugestellt wird. Es ist daher sinnvoll, den letzten Temperaturwert als Retained Message zu speichern.

##### Persistent Session

Wenn ein Client die Verbindung zum Broker unterbricht, gehen auch seine Abonnements verloren, d.h. er versäumt die publizierten Nachrichten.

Beim Verbindungsaufbau kann eine "persistent session" angefordert werden. Dazu muss das cleanSession Flag auf False gesetzt werden. Der Broker speichert dann alle versäumten Nachrichten und liefert sie nach.
Nicht jeder Broker unterstützt persistent Sessions!

#### ThingSpeak MQTT API

Der ThingSpeak MQTT Broker unterstützt nur den QoS Level 0 und keine persistent sessions.

Eine genaue Beschreibung der MQTT API ist unter
[<span class="underline">https://de.mathworks.com/help/thingspeak/mqtt-api.html</span>](https://de.mathworks.com/help/thingspeak/mqtt-api.html)
zu finden. Wir beschränken uns auf [<span class="underline">Publish to a Channel Field</span>](https://de.mathworks.com/help/thingspeak/mqtt-api.html).

##### Broker

Der ThingSpeak MQTT Broker ist unter der URL `mqtt.thingspeak.com` erreichbar. Der Port hängt vom Verbindungstyp ab.
![Thingspeak Broker Ports](./media/image52.png)

##### Topic

Jedes Feld in einem Channel ist ein eigenes Topic auf das geschrieben (published) oder das abonniert (subscribed) werden kann.
Das Topic ist folgendermaßen aufgebaut:  
`channels/channelID/publish/fields/field/<fieldnumber>/<apikey>`  
**Beispiel:**
![Thingspeak Topic](./media/image53.png)
![Thingspeak WriteAPI Key](./media/image54.png)

Ein neuer Temperaturwert muss also auf das Topic  
`channels/**381619**/publish/fields/**field1**/UPTTFJC3VVxxxxxx`  
publiziert werden.

##### Publish testen

Die Authentifizierung des Clients erfolgt auch hier wieder über API Keys. Für den Verbindungsaufbau muss einmalig zuerst ein MQTT API Key generiert werden.
![MQTT-API-Key](./media/image55.png)
![MQTT API Key](./media/image56.png)

Wir können nun mit einem Online MQTT Client einen Temperaturwert an unseren ThingSpeak Channel senden. Dieser Test empfiehlt sich, bevor wir mit unserem eigenen Programm vom LoPy aus publizieren.

Einen freien online MQTT Client gibt es unter:
[<span class="underline">http://www.hivemq.com/demos/websocket-client/</span>](http://www.hivemq.com/demos/websocket-client/).
Es gibt auch eine Vielzahl von MQTT Clients für Android oder iOS. Damit können Werte mit dem Smartphone an Thingspeak übertragen werden.

1. Für den Verbindungsaufbau wird der **MQTT API Key** benötigt.
   ![hivemqtt websocket client](./media/image57.png)
2. Wir publizieren nun einen neuen Temperaturwert 21.0 auf das Topic   
  `channels/381619/publish/fields/field1/UPTTFJC3VVxxxxxx`   
   Bitte die eigene `Channel Id` und eigenen `Write API Key` verwenden.   
  ![Publish](./media/image58.png)
3. Der neue Wert wird sofort in unserem Channel angezeigt:
   ![Channel View](./media/image59.png)
4. Die Verbindung können wir nun wieder trennen.
   ![HIVEMQ Disconnect](./media/image60.png)

> Publish ist damit erfolgreich getestet. Wir wissen nun, dass das Topic richtig ist und der API Key für den Verbindungsaufbau stimmt.

Falls es Probleme mit dem Verbindungsaufau gibt, könnte es auch an der Firewall liegen. Ein Test mit einem MQTT Client auf dem Smartphone und einer LTE Verbindung sollte in jedem Fall erfolgreich sein.

# LoPy – ThingSpeak

Mit den bereits getesteten Komponenten erstellen wir nun ein Programm für den LoPy, das die Temperaturwerte von der Sensorplatine einliest und über WLAN mit dem MQTT Protokoll an unseren ThingSpeak Channel sendet.

## WLAN Verbindung

Wir erweitern unser Programm in der Datei main.py mit dem WLAN
Verbindungsaufbau:

``` python
   from network import WLAN

   # connect to WLAN
   wlan = WLAN(mode=WLAN.STA)
   nets = wlan.scan()
   for net in nets:
      if net.ssid == 'WLAN-HTLB':
         print('Network found!')
         wlan.connect(net.ssid, auth=(WLAN.WPA2, 'xxxxxxxxxx'), timeout=5000)
         while not wlan.isconnected():
            machine.idle() # save power while waiting
         print('WLAN connection succeeded!')
         break
```

Die Bibliothek für WLAN Verbindungen wird geladen. Ein neuer WLAN Client wird initialisiert, das WLAN wird nach verfügbaren SSIDs gescannt, mit dem Netz „WLAN-HTLB“ wird eine Verbindung aufgebaut. „xxxxxxxxxx“ muss durch den WPA2 Schlüssel für das Netz "WLAN-HTLB" ersetzt werden.
Die `net.ssid` und der `WPA2 Schlüssel` müssen auf das verwendete WLAN-Netzwerk angepasst werden!

## MQTT Client

Das MQTT Protokoll muss als Bibliothek hinzugefügt werden. umqtt war bereits im GitHub Repository im Ordner „lib“ und wurde daher schon auf den LoPy snchronisiert.

```python
   from umqtt.robust import MQTTClient
   ...

   # Connect to thingspeak; password= MQTT API Key
   thingspeak = MQTTClient("fe058e3f82b94a968fb8bc10ccc", "52.5.134.229", port=1883, user="atkiot", password="MQTT_API_KEY")
   thingspeak.connect(clean_session=True)
```

In die Temperatur-Messschleife wird noch der MQTT Publish Befehl
eingefügt.

```python
   # publish temp
   thingspeak.publish("channels/347424/publish/fields/field1/WRITE_API_KEY",str(temp));
   time.sleep(30)
```

Das fertige Programm steht in der Datei main\_template.py und kann in die Datei main.py kopiert werden (Inhalt überschreiben oder vorher alles löschen).
Unbedingt notwendige Anpassungen:
   1. Die API Keys müssen durch die eigenen ThingSpeak Keys ersetzt werden\!
	2. CHANNEL_ID durch eignen ChannelID ersetzen.
	3. net.ssid und WLAN Passwort in wlan.connect an das eignen WLAN anpassen.
	4. Die clientID "fe058e3f82b94a968fb8bc10ccc" verändern, damit nicht zwei LoPy die geiche ID verwenden. Einfach ein paar beliebige Zeichen verändern. Nur die Ziffern 0-9 und die Buchstaben a-f verwenden.
	5. Falls vom DHCP Server keine DNS Adresse vergeben wird, muss in MQTTClient() die IP-Adresse von mqtt.thingspeak.com eingetragen werden.

```python
 import time
 import machine
 from network import WLAN
 from umqtt.robust import MQTTClient
 from lopy_max31865 import MAX31865

 # connect to WLAN
 wlan = WLAN(mode=WLAN.STA)
 nets = wlan.scan()
 for net in nets:
     if net.ssid == 'WLAN-HTLB':
         print('Network found!')
         wlan.connect(net.ssid, auth=(WLAN.WPA2, 'WLAN Passwort'), timeout=5000)
         while not wlan.isconnected():
             machine.idle() # save power while waiting
         print('WLAN connection succeeded!')
         break

 # create MAX31865 object
 rtd = MAX31865()

 # Connect to thingspeak; password= MQTT API Key
 thingspeak = MQTTClient("fe058e3f82b94a968fb8bc10ccc", "mqtt.thingspeak.com", port=1883, user="atkiot", password="MQTT_API_KEY")
 thingspeak.connect(clean_session=True)
 while True:
     temp = rtd.read()
     print('Temperatur: ',temp)
     # publish temp
     thingspeak.publish("channels/CHANNEL_ID/publish/fields/field1/WRITE_API_KEY",str(temp));
     time.sleep(30)
 thingspeak.disconnect()
```

Dateien **abspeichern** nicht vergessen.

Nach einem Sync/Upload sollte nun alle 30s ein Temperaturwert an unseren ThingSpeak Channel „motor\_1“ gesendet werden.

**Achtung:** Mit einem kostenlosen ThingSpeak Konto kann höchstens alle **15s** ein Update an den Channel gesendet werden. Bezahlte Konten dürfen einmal pro Sekunde updaten.

# Auswertung der Daten mit MATLAB und IFTTT

Die Temperaturmesswerte sind nun auf ThingSpeak in einem Channel gespeichert und können jetzt ausgewertet werden. Mit MATLAB und den Toolboxen verfügt die ThingSpeak Plattform über eine sehr leistungsfähige Auswertung.

## Neuer Channel für die Auswertung

Laut unserer Aufgabenstellung müssen wir den Unterschied zwischen zwei aufeinanderfolgenden Werten berechnen. Das Ergebnis schreiben wir in einen neuen Channel.

Warum schreiben wir nicht in ein anderes Feld im gleichen Channel?
Mit unserem Account dürfen wir nur alle 15s in den Channel schreiben.

1.  Temperaturwert in Feld 1 schreiben
2.  MATLAB Auswertung
3.  Ergebnis in ein Feld 2 schreiben

Die Auswertung ist sehr einfach und dauert weniger als 1s. Zwischen dem Schreibvorgang (1.) und (3.) vergehen weniger als 15s und wir würden eine Fehlermeldung erhalten.

Mit einem neuen Channel:

1.  Temperaturwert in Feld 1 Channel **motor\_1** schreiben
2.  MATLAB Auswertung
3.  Ergebnis in Feld 1 Channel **motor\_1\_calc** schreiben

### Neuen Channel *motor\_1\_calc* anlegen

Den Channel „motor\_1\_calc“ wie bereits unter "Neuen Channel erstellen" beschrieben erstellen.  
![](./media/image66.png)

### MATLAB Analysis

Wir erstellen nun ein MATLAB Skript für die Berechnung der
Temperaturänderung.
1. MATLAB Analysis anlegen
![](./media/image67.png)
2. Name „Delta\_Tu“
![](./media/image68.png)
3. MATLAB Code eingeben
```matlab
% gelesen. Die Änderung wird berechnet und in das Feld 1 channel motor_1_calc geschrieben

% Aus diesem Channel werden die Temperaturwerte gelesen
readChannelID = 381619;
% Tu Field ID
TuFieldID = 1;
% Channel Read API Key; siehe rechte Spalte
readAPIKey = 'READ_API_KEY';

% In diesen Channel wird das Delta_Tu geschrieben
writeChannelID = 383152;
writeAPIKey = 'WRITE_API_KEY';

% Die letzten 2 Temperaturwerte aus dem channel 'motor_1' lesen
tu = thingSpeakRead(readChannelID, 'Fields', TuFieldID, 'NumPoints', 2, 'ReadKey', readAPIKey);

% Wenn mindestens zwei Werte vorhanden sind,
% Differenz ausrechnen und in neuen channel schreiben
if length(tu) > 1
    delta_Tu = tu(2)-tu(1);
    display(delta_Tu, 'Temperaturänderung')
    thingSpeakWrite(writeChannelID, delta_Tu, 'writekey', writeAPIKey);
end
```

Download Programmcode:
[<span class="underline">Delta\_Tu.m</span>](./media/Delta_Tu.m)

### Reaktion auf neuen Temperaturwert

Über den Button „Save and Run“ kann das MATLAB Skript manuell gestartet werden. Das Skript sollte aber automatisch ausgeführt werden, sobald ein neuer Wert in den Channel „motor\_1“ geschrieben wird.
Die **React-App** kann auf verschieden Ereignisse reagieren. Wir legen uns eine React App an, die immer das vorher erstellte MATLAB Skript *Delta\_Tu* startet, wenn ein neuer Temperaturwert in den Channel geschrieben wird.

On Data Insertation Action: Das vorher erstellte MATLAB Skript
*Delta\_Tu* ausführen.
![](./media/image70.png)

### Reaktion auf große Temperaturänderung

Sobald ein neuer Temperaturwert eintrifft, wird mit dem MATLAB Skript *Delta\_Tu*  die Temperaturdifferenz berechnet und in den Channel *motor\_1\_calc* geschrieben. Wir können mit einer weiteren React-App diesen Wert überwachen und, wenn er einen festgelegten Maximalwert überschreitet, eine Aktion auslösen.  
ThingSpeak stellt einige Aktionen bereit. Sehr mächtig ist die Aktion **ThingHTTP**. Mit ThingHTTP kann ein beliebiger HTTP-Request generiert werden.

Laut unserer Aufgabenstellung möchten wir eine E-Mail versenden, wenn die Temperaturänderung zu groß ist. ThingSpeak kennt keine eingebaute Aktion „E-Mail versenden“. Die Internetplattform IFTTT kann uns helfen. Mit ThingHTTP können wir ein Ereignis an IFTTT senden und dort das Senden einer E-Mail auslösen.

# IFTTT (If This Than That)

IFTTT ist eine Internet-Plattform, die Ereignisse mit Aktionen
verbindet, E-Mail versenden ist eine von sehr vielen möglichen Aktionen.

## IFTTT Konto anlegen

1. [<span class="underline">https://ifttt.com/</span>](https://ifttt.com/)
  ![](./media/image71.png)
2. Konto anlegen: Sign up
  ![](./media/image72.png)
3. Anmeldedaten eingeben
  ![IFTTT Anmeldedaten](./media/image73.png)

## IFTTT Applet erstellen

1. Ein neues Applet erstellen
![](./media/image74.png)
2. Bedingung die erfüllt sein muss, damit die Aktion ausgeführt wird
![](./media/image75.png)
3. Webhook-Service auswählen
![](./media/image76.png)
4. Webhook konfigurieren
![](./media/image77.png) ![](./media/image78.png)
5. Einen Namen für Trigger-Ereignis wählen z. B. *Motor1TuRisingFast*   
![](./media/image79.png)
6. Aktion definieren
![](./media/image80.png)
7. Aus einer Vielzahl von Aktionen auswählen. Wir wählen E-Mail.
![](./media/image81.png) ![](./media/image82.png)
8. E-Mail Inhalt festlegen: „Warnung: Schneller Temperaturanstieg im Motor 1“   
![](./media/image83.png) ![](./media/image84.png) ![](./media/image85.png)

## IFTTT mit ThingSpeak verbinden

Wie können wir nun das Trigger-Ereignis *Motor1TuRisingFast* von ThingSpeak aus mit ThingHTTP an IFTTT senden? Die IFTTT Webhooks Dokumentation hilft uns weiter.
### Webhooks Dokumentation

1. Services auswählen
![](./media/image86.png)
2. Dokumentation öffnen
![](./media/image87.png)
3. API-Key und https request nachschauen
![](./media/image88.png)
`{event}`muss durch den gewünschten Trigger-Event (hier: *Motor1TuRisingFast*) ersetzt werden:
`https://maker.ifttt.com/trigger/Motor1TuRisingFast/with/key/pXXuT_od2Ifxxxxxxxxxxxx`    
`pXXuT_od2Ifxxxxxxxxxxxx...` ist der API-Key und <!-- TOC depthFrom:1 depthTo:3 withLinks:1 updateOnSave:1 orderedList:0 -->

	- [Komponenten einer IoT Anwendung](#komponenten-einer-iot-anwendung)
	- [Übersicht Datenfluss und Kommunikation](#übersicht-datenfluss-und-kommunikation)
- [Das „Ding“: LoPy](#das-ding-lopy)
	- [LoPy Developement Boards](#lopy-developement-boards)
	- [Expansion Board](#expansion-board)
	- [Atom Editor mit pymakr plug-in](#atom-editor-mit-pymakr-plug-in)
- [Temperaturmessung](#temperaturmessung)
	- [Adafruit Interface Board](#adafruit-interface-board)
		- [Verdrahtung LoPy - MAX31865 Interface Board](#verdrahtung-lopy-max31865-interface-board)
		- [Verdrahtung mit Steckplatine](#verdrahtung-mit-steckplatine)
	- [SPI-Bus Interface](#spi-bus-interface)
		- [Eigenschaften](#eigenschaften)
		- [LoPy SPI-Bus](#lopy-spi-bus)
		- [Klasse lopy\_max31865](#klasse-lopymax31865)
		- [Anwendung der Klasse lopy\_max31865](#anwendung-der-klasse-lopymax31865)
	- [Temperaturwerte auslesen](#temperaturwerte-auslesen)
		- [Bibliothek „lopy\_max31865“ auf den LoPy übertragen.](#bibliothek-lopymax31865-auf-den-lopy-übertragen)
		- [REPL](#repl)
		- [Python Programm](#python-programm)
- [ThingSpeak](#thingspeak)
	- [ThingSpeak Konto einrichten](#thingspeak-konto-einrichten)
	- [ThingSpeak Channel](#thingspeak-channel)
		- [Neuen Channel erstellen](#neuen-channel-erstellen)
	- [ThingSpeak API](#thingspeak-api)
		- [REST API](#rest-api)
		- [MQTT API](#mqtt-api)
- [LoPy – ThingSpeak](#lopy-thingspeak)
	- [WLAN Verbindung](#wlan-verbindung)
	- [MQTT Client](#mqtt-client)
- [Auswertung der Daten mit MATLAB und IFTTT](#auswertung-der-daten-mit-matlab-und-ifttt)
	- [Neuer Channel für die Auswertung](#neuer-channel-für-die-auswertung)
		- [Neuen Channel *motor\_1\_calc* anlegen](#neuen-channel-motor1calc-anlegen)
		- [MATLAB Analysis](#matlab-analysis)
		- [Reaktion auf neuen Temperaturwert](#reaktion-auf-neuen-temperaturwert)
		- [Reaktion auf große Temperaturänderung](#reaktion-auf-groe-temperaturänderung)
- [IFTTT (If This Than That)](#ifttt-if-this-than-that)
	- [IFTTT Konto anlegen](#ifttt-konto-anlegen)
	- [IFTTT Applet erstellen](#ifttt-applet-erstellen)
	- [IFTTT mit ThingSpeak verbinden](#ifttt-mit-thingspeak-verbinden)
		- [Webhooks Dokumentation](#webhooks-dokumentation)
		- [ThingHTTP Request erstellen](#thinghttp-request-erstellen)
		- [React erstellen](#react-erstellen)
- [Zusammenfassung](#zusammenfassung)

<!-- /TOC -->ist für jeden IFTTT Benutzer unterschiedlich.
4. Testen   
„Test It“ löst den Trigger aus, die Aktion „E-Mail senden“ wird ausgeführt. Innerhalb weniger Sekunden sollte die E-Mail von IFTTT eintreffen. Man könnte auch die https-Zeile in den Browser kopieren und die Seite abrufen.

### ThingHTTP Request erstellen

Das Format für den Web-Request ist jetzt bekannt und wir können in ThingSpeak die ThingHTTP App konfigurieren.  
![](./media/image89.png)

### React erstellen

Immer wenn die Temperaturerhöhung zwischen aufeinanderfolgenden Werten größer als 20°C ist, soll ThingHTTP einen Web-Request an IFTTT senden, IFTTT sendet dann eine E-Mail.  
![](./media/image90.png)
![](./media/image93.png)

In der Abbildung ist ein *temp_delta* von 10°C eingetragen, das Austesten wird dadurch erleichtert.

Unsere erste IoT Anwendung ist nun fertig!

# Zusammenfassung
In diesem Workshop haben wir alle Komponenten einer IoT-Anwendung kennengelernt.  

- Sensor: PT100 Temperatursensor
- Interface: SPI
- Mikrocontroller: LoPy
- Netzwerkzugang: WLAN
- Cloud Service: ThingSpeak
- Analysieren: MATLAB
- Visualisieren: ThingSpeak
- Reagieren: React / IFTTT

Jede dieser Komponenten kann einfach ausgetauscht werden, da sie über Standardprotokolle miteinander in Verbinung stehen.  
Beispiele:  

- Sensor: einfache Schalter, DS18B20 Temperatursensor mit on-wire Schnittstelle, Barometer, 9DOF-Sensor (BNO055), GPS
- Interface: I2C Bus
- Mikrocontroller: Arduino, ESP32, C32000, Raspberry
- Die WLAN-Anbindung kann leicht durch eine LoRaWAN-Anbindung an TheThingsNetwork (TTN) ausgetauscht werden. Dann ist es aber einfacher,  TagoIO als Cloud Service zu verwenden.
- Cloud Service: TagoIO, ThingWorx, Linuxserver mit InfluxDB
- Visualisieren: grafana
- Reagieren: Zapier


Zusammengestellt von Kurt Albrecht, HTL Bregenz.  
Letzte Änderung: 2019-04-28
