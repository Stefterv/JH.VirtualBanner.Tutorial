# Baue dein eigenes Captive Portal mit dem Raspberry Pi Pico

https://tinyurl.com/jhcaptive

🇩🇪 🇩🇪 Deutsche Version: [README.de.md](./README.de.md)

### What do you need
- Laptop (Windows, macOS or Linux)
- VSCode
- Raspberry PI Pico W or Raspberry PI Pico 2
- USB Cable

### Setting up vscode
- Install VS Code - https://code.visualstudio.com/download
- Install the pico extension - https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico
    - Search for pico in the extensions

![Search for the Pico extension](./screenshots/01-extension-search.png)
![Select the Raspberry Pi Pico extension](./screenshots/02-extension-install.png)

- Open the Pico tools in VS Code

![Find the Pico tooling in VS Code](./screenshots/03-extension-find.png)

### Setting up the pico
- Connect the pico with the usb cable to the computer
- Create a new Pico project in VS Code

![Create a new project](./screenshots/04-project-new.png)

- Choose a project name

![Choose a project name](./screenshots/06-project-naming.png)

- Trust the project workspace if prompted

![Trust the project workspace](./screenshots/07-project-trust.png)

- The pico might be in BOOTSEL mode, click Yes to install the firmware

![Boot the Pico in BOOTSEL mode](./screenshots/08-pi-bootsel.png)

- Flash the Pico firmware from VS Code

![Flash the Pico firmware](./screenshots/09-pi-flash.png)

- Wait until the board is ready

![Pico is ready in VS Code](./screenshots/10-ready.png)

- Open the `blink.py`
- Click run in the bottom corner

![Run the project on the Pico](./screenshots/11-run.png)

- You should see the onboard LED blinking

![Blink example running](./screenshots/12-blink.png)

### Installing phew
- Open the repository https://github.com/pimoroni/phew/releases/tag/v0.0.3

![Open the phew repository](./screenshots/13-phew-repo.png)

- Goto releases

![Open the releases page](./screenshots/14-phew-release.png)

- Download the zip file
- Unzip the file
- copy the `phew` folder to the main folder of your project

![Locate the phew folder](./screenshots/15-phew-folder.png)
![Copy the phew folder into the project](./screenshots/16-phew-copy.png)

- Right click in the explorer and upload to pico

![Upload the phew folder to the Pico](./screenshots/17-phew-upload.png)

### Captive portal
- create `main.py` !! SCREENSHOT !!
```python
from phew import access_point, server, dns

ap = access_point("Pico W Captive")
```
- run, if you now check the available wifi networks on your device you should see "Pico W Captive"
- add the following to existing from statement
```python
from phew.server import redirect

@server.route("/", methods=['GET'])
def index(request):
    """ Render the Index page"""
    if request.method == 'GET':
        print("Request received")
        return "Hello World"

@server.catchall()
def catch_all(request):
    return redirect("http://hello.world/")

# Grab the IP address and store it
ip = ap.ifconfig()[0]
# Catch all requests and reroute them
dns.run_catchall(ip)
server.run()
```
- this will create a captive portal, if you try to open any website it will redirect you to http://hello.world/, show `Hello World` on the page and print `Request received` in the console

### Customisation
- At this point you should have a working captive portal, you can customise it in a few ways:
- You can change the name of the wifi network by changing the string in `access_point("Pico W Captive")`
- You can change the response by changing the string in `return "Hello World"`
- You can change the redirect url by changing the string in `redirect("http://hello.world/")`

### Website
- create `index.html`
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
- change the index route to serve the html file
```python
@server.route("/", methods=['GET'])
def index(request):
    """ Render the Index page"""
    if request.method == 'GET':
        with open("index.html", "r") as file:
            html = file.read()
        return html
```

!! Upload files to Pico !!


### Hardware
- The Pico W has an onboard LED, you can control it using the `machine` module
```python
from machine import Pin
led = Pin("LED", Pin.OUT)
```
- add a new route to toggle the LED 

```python
@server.route("/blink", methods=['GET'])
def blink(request):
    """ Render the Index page"""
    if request.method == 'GET':
        led.toggle()

        with open("index.html", "r") as file:
            html = file.read()
        return html
```
- Add a button to the html file to toggle the LED, Under the &lt;h1&gt; tag
```html
<a href="/blink">Toggle LED</a>
```
- Now when you click the button it will toggle the LED on the pico


### Troubleshooting





### Static Files
Add the following route to your project to serve static files from the pico. You can then access these files at `http://<pico_ip>/static/<file_name>`

```python
@server.route("/static/<file>", methods=['GET'])
def serve_file(request, file):
    """ Rendert die Startseite """
    if request.method == 'GET':
        try:
            with open(file, "r") as f:
                content = f.read()
            return content
        except FileNotFoundError:
            return "File not found", 404

```