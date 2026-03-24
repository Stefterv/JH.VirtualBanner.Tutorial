# Raspberry PI - Title Here
- 

### What do you need
- Laptop (Windows, macOS or Linux)
- VSCode
- Raspberry PI Pico W or Raspberry PI Pico 2
- USB Cable

### Setting up vscode
- Create a new profile?!
- Install the pico extension - https://marketplace.visualstudio.com/items?itemName=raspberry-pi.raspberry-pi-pico
    - Search for pico in the extensions
![](./screenshots/Screenshot%202026-03-24%20at%2017.59.14.png)
![]

### Setting up the pico
- Connect the pico with the usb cable to the computer
- Open the blink.py
- Click run in the bottom corner
- You should see the onboard LED blinking


### Installing phew
- Open the repository https://github.com/pimoroni/phew/releases/tag/v0.0.3
- Goto releases
- Download the zip file
- Unzip the file
- copy the `phew` folder to the main folder of your project
- right click in the explorer and upload to pico

### Captive portal
- create `main.py`
```python
from phew import access_point

ap = access_point("Pico W Captive")
```
- run, if you now open your mobile phone and look for wifi networks you should see "Pico W Captive"
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
- this will create a captive portal, if you try to open any website it will redirect you to http://hello.world/, show Hello World on the page and print "Request received" in the console

### Customisation
- You can change the name of the wifi network by changing the string in `access_point("Pico W Captive")`
- You can change the response by changing the string in `return "Hello World"`
- You can change the redirect url by changing the string in `redirect("http://hello.world/")`

### Troubleshooting