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
```diff
- from phew import access_point
+ from phew import access_point, dns

ap = access_point("Pico W Captive")
+ # Grab the IP address and store it
+ logging.info(f"starting DNS server on {ip}")
+ # # Catch all requests and reroute them
+ ip = ap.ifconfig()[0]
+ dns.run_catchall(ip)
```