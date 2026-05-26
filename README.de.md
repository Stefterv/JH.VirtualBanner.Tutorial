# Baue dein eigenes Captive Portal mit dem Raspberry Pi Pico

https://tinyurl.com/jhcaptive

🇬🇧 🇬🇧 Englische Version: [README.md](./README.md)

### Was brauchst du?
- Laptop (Windows, macOS oder Linux)
- VS Code
- Raspberry Pi Pico W oder Raspberry Pi Pico 2
- USB-Kabel

### VS Code einrichten
- Installiere VS Code: https://code.visualstudio.com/download
- Installiere die Pico-Erweiterung: https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico
    - Suche in den Erweiterungen nach `pico`

![Suche nach der Pico-Erweiterung](./screenshots/01-extension-search.png)
![Wähle die Raspberry Pi Pico-Erweiterung aus](./screenshots/02-extension-install.png)

- Öffne die Pico-Tools in VS Code

![Finde die Pico-Tools in VS Code](./screenshots/03-extension-find.png)

### Den Pico einrichten
- Verbinde den Pico mit dem USB-Kabel mit deinem Computer
- Erstelle in VS Code ein neues Pico-Projekt

![Erstelle ein neues Projekt](./screenshots/04-project-new.png)

- Wähle einen Projektnamen

![Wähle einen Projektnamen](./screenshots/06-project-naming.png)

- Vertraue dem Arbeitsbereich, falls VS Code danach fragt

![Vertraue dem Arbeitsbereich](./screenshots/07-project-trust.png)

- Der Pico ist eventuell im BOOTSEL-Modus. Klicke auf Yes, um die Firmware zu installieren

![Starte den Pico im BOOTSEL-Modus](./screenshots/08-pi-bootsel.png)

- Flashe die Pico-Firmware aus VS Code

![Flashe die Pico-Firmware](./screenshots/09-pi-flash.png)

- Warte, bis das Board bereit ist

![Pico ist in VS Code bereit](./screenshots/10-ready.png)

- Öffne `blink.py`
- Klicke unten in VS Code auf Run

![Starte das Projekt auf dem Pico](./screenshots/11-run.png)

- Die Onboard-LED sollte jetzt blinken

![Blink-Beispiel läuft](./screenshots/12-blink.png)

### phew installieren
- Öffne das Repository: https://github.com/pimoroni/phew/releases/tag/v0.0.3

![Öffne das phew-Repository](./screenshots/13-phew-repo.png)

- Gehe zu den Releases

![Öffne die Releases-Seite](./screenshots/14-phew-release.png)

- Lade die ZIP-Datei herunter
- Entpacke die Datei
- Kopiere den Ordner `phew` in den Hauptordner deines Projekts

![Finde den phew-Ordner](./screenshots/15-phew-folder.png)
![Kopiere den phew-Ordner in das Projekt](./screenshots/16-phew-copy.png)

- Klicke im Explorer mit der rechten Maustaste und lade den Ordner auf den Pico hoch

![Lade den phew-Ordner auf den Pico hoch](./screenshots/17-phew-upload.png)

### Captive Portal
- Erstelle `main.py` !! SCREENSHOT !!
```python
from phew import access_point, server, dns

ap = access_point("Pico W Captive")
```
- Starte das Projekt. Wenn du jetzt die verfugbaren WLAN-Netzwerke auf deinem Gerat ansiehst, solltest du `Pico W Captive` sehen
- Erweitere die bestehende `from`-Anweisung um Folgendes
```python
from phew.server import redirect

@server.route("/", methods=['GET'])
def index(request):
    """ Rendert die Startseite """
    if request.method == 'GET':
        print("Request received")
        return "Hello World"

@server.catchall()
def catch_all(request):
    return redirect("http://hello.world/")

# Die IP-Adresse holen und speichern
ip = ap.ifconfig()[0]
# Alle Anfragen abfangen und weiterleiten
dns.run_catchall(ip)
server.run()
```
- Dadurch entsteht ein Captive Portal. Wenn du versuchst, irgendeine Website zu offnen, wirst du zu `http://hello.world/` weitergeleitet, siehst `Hello World` im Browser und im Terminal wird `Request received` ausgegeben

### Anpassungen
- Jetzt solltest du ein funktionierendes Captive Portal haben. Du kannst es auf mehrere Arten anpassen:
- Du kannst den Namen des WLAN-Netzwerks andern, indem du den String in `access_point("Pico W Captive")` veranderst
- Du kannst die Antwort andern, indem du den String in `return "Hello World"` veranderst
- Du kannst die Weiterleitungs-URL andern, indem du den String in `redirect("http://hello.world/")` veranderst

### Website
- Erstelle `index.html`
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
	<title>Virtual Banner</title>
    <style>
		body{margin:0;min-height:100vh;display:grid;place-items:center;font-family:sans-serif;background:#f5f7fa}
		main{width:min(92vw,420px);background:#fff;padding:24px;border-radius:12px;text-align:center;box-shadow:0 8px 24px #0001}
		h1{margin:0 0 10px;font-size:1.4rem}
		p{margin:0 0 18px;color:#555}
		a{display:inline-block;padding:10px 16px;border-radius:8px;background:#0077ff;color:#fff;text-decoration:none}
	</style>
</head>
<body>
	<main>
		<h1>Hotspot</h1>
	</main>
</body>
</html>
```
- Passe die `index`-Route so an, dass sie die HTML-Datei ausliefert
```python
@server.route("/", methods=['GET'])
def index(request):
    """ Rendert die Startseite """
    if request.method == 'GET':
        with open("index.html", "r") as file:
            html = file.read()
        return html
```

!! Dateien auf den Pico hochladen !!


### Hardware
- Der Pico W hat eine eingebaute LED, die du mit dem Modul `machine` steuern kannst
```python
from machine import Pin
led = Pin("LED", Pin.OUT)
```
- Fuge eine neue Route hinzu, um die LED umzuschalten

```python
@server.route("/blink", methods=['GET'])
def blink(request):
    """ Rendert die Startseite """
    if request.method == 'GET':
        led.toggle()

        with open("index.html", "r") as file:
            html = file.read()
        return html
```
- Fuge einen Button zur HTML-Datei hinzu, unterhalb des `<h1>`-Tags
```html
<a href="/blink">LED umschalten</a>
```
- Wenn du jetzt auf den Button klickst, wird die LED auf dem Pico umgeschaltet


### Fehlerbehebung