---
layout: post
title: PRIMUS_Blog-III
subtitle: Web-server code
header-img: img/BlogPrimus/images/blog3/thumbnail_Blog3.jpeg
---



# PRIMUS-III

Hello there! I'm excited to share the final installment in our PRIMUS series, where we'll dive into the technical aspects that bring this project to life. If you're eager to access the complete code and schematics, you can find them at this [link](https://github.com/seeker316/Primus). 
But before we get into the heavy stuff, let’s start with something simple—how does PRIMUS communicate? How are we able to control this robot wirelessly from our phones or laptops? The magic happens thanks to the brains of PRIMUS: the ESP32. It is a robust and cost-effective microcontroller that combines Wi-Fi connectivity with real-time processing capabilities, as I mentioned in the first [blog](https://seeker316.github.io/2024/04/19/Primus/). This allows it to handle both the remote control interface and motor control simultaneously, making it the perfect choice for our project. 

Let's start with seomething simple lets uncover how is the communication taking place, how are we able to control the robot with our phones or laptops wirelessly. The answer lies in the brains of PRIMUS, ESP32. The ESP32 is a powerful and low cost microcontroller that can combine Wi-Fi connectivity and real-time processing capabilities, allowing it to handle both the remote control interface and motor control simultaneously. Making it best suitable for our project.

The ESP32 is set up as both a Wi-Fi-connected HTTP server and a WebSocket server, enabling two forms of communication: traditional web access and real-time data exchange. Here's how the remote control and communication happen over the network:

1. The ESP32 connects to a local Wi-Fi network using the credentials you provide. Once connected, it becomes an active participant in the network, similar to any other device (like your phone or computer). 
2. The router assigns an IP address to the ESP32, making it reachable on the network. This allows devices on the same network to communicate with it.
The ESP32 hosts a web server that serves an interface (likely a control panel) accessible through a browser on a phone or computer. By typing the ESP32’s local IP address into a browser, users can access a webpage served by the ESP32. 
3. While the HTTP server provides the web interface of the controller GUI, the actual control happens through WebSockets. WebSockets allow bi-directional, real-time communication between the browser and the ESP32. 

![communication network](/img/BlogPrimus/images/blog3/primus_network_Blog3.png)

Unlike HTTP, which requires a new request for each action, WebSocket keeps a connection open between the client (browser) and server (ESP32) after the initial handshake. This means data can be sent and received continuously without delays. As you interact with the control panel (e.g., moving a virtual joystick or pressing buttons), your browser sends messages (commands) instantly to the ESP32 over the WebSocket connection.
Each message contains instructions for the motors, like speed and direction, encoded as data, which is used by the esp32 to control PRIMUS accordingly.

Now that we've gone through how the communication takes place let's go through the code,

### Including Necessary Libraries
- **WiFi.h**: Library for ESP32-wifi capabilities
- **ArduinoWebsockets.h**: Provides WebSocket functionality.
- **ESPAsyncWebServer.h**: Handles the HTTP server asynchronously.
- **Adafruit_NeoPixel.h**: Manages the NeoPixel LED strip
- **web.h**: custom header file that contains HTML

```
#include <WiFi.h>
#include <ArduinoWebsockets.h> 
#include <ESPAsyncWebServer.h>
#include <Adafruit_NeoPixel.h> 
#include "web.h" 
```
### Setting Up NeoPixel LED Strip
- **RGB_PIN**: This defines GPIO 15 as the pin controlling the NeoPixel LED strip.
- **NUM_PIXELS**: The number of LEDs in the strip is 28.
- **NeoPixel**: An instance of the Adafruit_NeoPixel class is initialized, setting the number of LEDs, the data pin, and the communication protocol.
- **randomColor**: Generates a random color for use later, by picking random values for the red, green, and blue components (between 0 and 255).
```
#define RGB_PIN 15
#define NUM_PIXELS 28
Adafruit_NeoPixel NeoPixel(NUM_PIXELS,RGB_PIN, NEO_GRB + NEO_KHZ800);
uint32_t randomColor = NeoPixel.Color(random(256), random(256), random(256));
```
### Wi-Fi Credentials and Initialization
```
const char* ssid = "********"; //Enter SSID
const char* password = "********"; //Enter Password

```
### Initializing WebSocket and HTTP Servers
- **WebsocketsServer**: A WebSocket server instance is created.

- **AsyncWebServer**: An HTTP server instance is created. It listens on port 80 (the default port for HTTP).
```
using namespace websockets;
WebsocketsServer server;
AsyncWebServer webserver(80);

```
### Motor Pin, PWM and Neo-pixel Setup
- **Pin1 and Pin2**: These control the direction of the motor, determining whether it moves forward or backward.

- **Enable Pins**: Responsible for motor speed control using PWM (Pulse Width Modulation). By switching the motor on and off rapidly, we can achieve the desired velocity. 
- **PWM parameters setup**: In this step, we configure the PWM frequency, channel, and resolution, with the duty cycle (motor speed) initialized to 200. For more details, refer to Blog-II.

- **pinMode**: Configures the motor control pins as outputs and sets up PWM for the enable pins, allowing the motor speed to be controlled by adjusting the duty cycle of the PWM signal.

- **ledcSetup & ledcAttachPin**: Configures the PWM channels and attaches them to the enable pins to manage motor speed.

- **NeoPixel.begin()**: Initializes the NeoPixel LED strip.

- **NeoPixel.setBrightness(50)**: Sets the brightness of the LEDs to 50 (on a scale from 0 to 255).

```
int M1value, M2value, M3value, commaIndex;

int motor1Pin1 = 14; 
int motor1Pin2 = 12;

int motor2Pin1 = 26; 
int motor2Pin2 = 25;

int motor3Pin1 = 32; 
int motor3Pin2 = 4; 

int enable1Pin = 13; 
int enable2Pin = 27; 
int enable3Pin = 33;

const int freq = 30000;
const int pwmChannel = 0;
const int resolution = 8;
int dutyCycle = 200;

void setup()
{
  // Motor pin modes
  pinMode(motor1Pin1, OUTPUT);
  pinMode(motor1Pin2, OUTPUT);
  pinMode(enable1Pin, OUTPUT);
  pinMode(motor2Pin1, OUTPUT);
  pinMode(motor2Pin2, OUTPUT);
  pinMode(enable2Pin, OUTPUT);
  pinMode(motor3Pin1, OUTPUT);
  pinMode(motor3Pin2, OUTPUT);
  pinMode(enable3Pin, OUTPUT);

  NeoPixel.begin();
  NeoPixel.setBrightness(50);

  ledcSetup(pwmChannel, freq, resolution);
  ledcAttachPin(enable1Pin, pwmChannel);
  ledcAttachPin(enable2Pin, pwmChannel);
  ledcAttachPin(enable3Pin, pwmChannel);
```
### Wi-Fi Connection, HTTP Server and WebSocket Server Setup
- **Connecting to Wi-Fi**: The ESP32 is connected to a Wi-Fi network using the credentials provided earlier. It repeatedly checks the connection status and, once connected, prints the local IP address to the serial monitor. This IP address is what you use to access the server from your browser.

- **Setting up the HTTP Server**: The HTTP server is configured to serve the webpage when a user accesses the root URL (/). The server responds with a precompressed HTML file in gzip format. This compressed format helps reduce the file size, making the webpage load faster. This file is served with a *Content-Encoding: gzip header*, so the browser knows to decompress it when displaying the webpage. The gzip file, index_html_gz, is defined in the web.h file as a byte array, like this:
```
const uint8_t index_html_gz[] = { 0x1f, 0x8b, 0x08, ... };
```
- **WebSocket Server Setup**: The WebSocket server is initialized on port 82. This server allows real-time, two-way communication between the browser and the ESP32.
```
  Serial.begin(9600);
  Serial.print("Connecting to WiFi...");
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("Connected to WiFi!");
  Serial.print("Local IP address: ");
  Serial.println(WiFi.localIP());

  webserver.on("/", HTTP_GET, [](AsyncWebServerRequest * request) {
    AsyncWebServerResponse *response = request->beginResponse_P(200, "text/html", index_html_gz, sizeof(index_html_gz));
    response->addHeader("Content-Encoding", "gzip");
    request->send(response);
  });
  webserver.begin();
  server.listen(82);
  Serial.print("Is server live? ");
  Serial.println(server.available());
}
```
### Handling WebSocket Messages
This is a callback function, which is triggered when a websocket message is recieved. When a WebSocket message contains three values separated by commas in a string. These values represent the speeds for three motors (M1value, M2value, M3value).
The values are extracted from the message and converted to integers. which is then used to control the motors depending on the value:
- If positive, the motor moves forward.
- If negative, the motor moves backward.
- If zero, the motor stops.
```
void handle_message(WebsocketsMessage msg) {
  commaIndex = msg.data().indexOf(',');
  int secondCommaIndex = msg.data().indexOf(',', commaIndex + 1);
  M1value = msg.data().substring(0, commaIndex).toInt();
  M2value = msg.data().substring(commaIndex + 1, secondCommaIndex).toInt();
  M3value = msg.data().substring(secondCommaIndex + 1).toInt();

  if (M1value > 0) {
    digitalWrite(motor1Pin1, HIGH);
    digitalWrite(motor1Pin2, LOW);
    analogWrite(enable1Pin, M1value);
  } else if (M1value < 0) {
    digitalWrite(motor1Pin1, LOW);
    digitalWrite(motor1Pin2, HIGH);
    analogWrite(enable1Pin, abs(M1value));
  } else {
    digitalWrite(motor1Pin1, LOW);
    digitalWrite(motor1Pin2, LOW);
    analogWrite(enable1Pin, 0);
  }
}
```
### Main Loop
This loop handles the LED states and checks for incoming WebSocket connections, allowing the bot to transition between idle (red) and active (green/random) colors based on the connection status to indicate the current state of the bot:
- **Red**: The bot is not connected to any user for control. All LEDs display red.
- **Green**: The bot is connected to a user. Once connected, the green color transitions to a randomly generated color as the robot starts moving, reflecting the bot's active state.

Additionally, when a WebSocket client connects, the bot handles motor control based on the incoming messages from the client.
```
void loop()
{
  NeoPixel.clear();
  for (int pixel = 0; pixel < NUM_PIXELS; pixel++) {
    NeoPixel.setPixelColor(pixel, NeoPixel.Color(255, 0, 0));
    NeoPixel.show();
    delay(10);
  }
  auto client = server.accept();
}
```
### HTML WEB SERVER CODE
This code creates a web interface for controlling motors using a joystick. It is zipped into gzip format and is pasted in the web.h file. This code contains the following constituents

- **HTML Structure**:
A background image is set, and a title ("CASTOR Jr.") is provided for the page.
A container (joyDiv) holds the virtual joystick, which users can interact with.
The motor values (for 3 motors) and joystick position (X, Y) are displayed in real-time.

![web-server controller GUI](/img/BlogPrimus/images/blog3/remote_gui_Blog3.jpeg)

- **CSS Styling**:
The page has a responsive layout with a background image.
The joystick is styled as a yellow circle that changes appearance on hover and click.

- **JavaScript Functionality**:
A joystick is implemented using the JoyStick function, which captures touch or mouse input and calculates the joystick's position (X, Y) and direction. WebSocket communication is established to send motor values to a server. A function getfuerza() calculates motor speeds based on joystick input using a linear transformation to adapt joystick values to motor speeds for a three-wheeled omniwheel robot. The calculations include specific coefficients to achieve the desired motion, mapping joystick input values to motor commands that correspond to the robot's movement requirements. These points and the derivation behind the math was thoroughtly discussed in the last blog. The joystick is updated every 300 milliseconds to send motor control commands via WebSocket, limiting message frequency to prevent overloading.

```
remove # from code
#<!DOCTYPE html>

<html>

<head>
    <meta name='viewport' content='width=device-width, initial-scale=1.0, user-scalable=no' />
    <meta charset="utf-8" />
    "<title>CASTOR Jr.</title>";

    <style>
        html {
          background-image: url('https://github.com/seeker316/CastorJr/raw/main/webserverbackground.jpg');
          background-size: cover;
          background-position: center;
          height: 100%;
        }

        #container {
            width: 100%;
            height: 49vh;
            background-color: #636363;
            display: flex;
            align-items: center;
            justify-content: center;
            overflow: hidden;
            border-radius: 7px;
            touch-action: none;
        }

        #item {
            width: 100px;
            height: 100px;
            background-color: rgb(245, 230, 99);
            border: 10px solid rgba(136, 136, 136, .5);
            border-radius: 50%;
            touch-action: none;
            user-select: none;
        }

        #item:active {
            background-color: rgba(168, 218, 220, 1.00);
        }

        #item:hover {
            cursor: pointer;
            border-width: 20px;
        }

        #motorValues {
            position: fixed;
            top: 10px;
            right: 10px;
            color: white;
            font-family: Arial, sans-serif;
            font-size: 14px;
        }
    </style>


    <script type="text/javascript">
        var JoyStick = function (t, e) { var i = void 0 === (e = e || {}).title ? "joystick" : e.title, n = void 0 === e.width ? 0 : e.width, o = void 0 === e.height ? 0 : e.height, h = void 0 === e.internalFillColor ? "#00AA00" : e.internalFillColor, r = void 0 === e.internalLineWidth ? 2 : e.internalLineWidth, d = void 0 === e.internalStrokeColor ? "#003300" : e.internalStrokeColor, a = void 0 === e.externalLineWidth ? 2 : e.externalLineWidth, l = void 0 === e.externalStrokeColor ? "#008000" : e.externalStrokeColor, c = document.getElementById(t), u = document.createElement("canvas"); u.id = i, 0 == n && (n = c.clientWidth), 0 == o && (o = c.clientHeight), u.width = n, u.height = o, c.appendChild(u); var s = u.getContext("2d"), f = 0, v = 2 * Math.PI, g = (u.width - 110) / 2, w = g + 5, C = g + 30, m = u.width / 2, p = u.height / 2, L = u.width / 10, E = -1 * L, S = u.height / 10, k = -1 * S, W = m, G = p; function x() { s.beginPath(), s.arc(m, p, C, 0, v, !1), s.lineWidth = a, s.strokeStyle = l, s.stroke() } function y() { s.beginPath(), W < g && (W = w), W + g > u.width && (W = u.width - w), G < g && (G = w), G + g > u.height && (G = u.height - w), s.arc(W, G, g, 0, v, !1); var t = s.createRadialGradient(m, p, 5, m, p, 200); t.addColorStop(0, h), t.addColorStop(1, d), s.fillStyle = t, s.fill(), s.lineWidth = r, s.strokeStyle = d, s.stroke() } "ontouchstart" in document.documentElement ? (u.addEventListener("touchstart", function (t) { f = 1 }, !1), u.addEventListener("touchmove", function (t) { t.preventDefault(), 1 == f && (W = t.touches[0].pageX, G = t.touches[0].pageY, W -= u.offsetLeft, G -= u.offsetTop, s.clearRect(0, 0, u.width, u.height), x(), y()) }, !1), u.addEventListener("touchend", function (t) { f = 0, W = m, G = p, s.clearRect(0, 0, u.width, u.height), x(), y() }, !1)) : (u.addEventListener("mousedown", function (t) { f = 1 }, !1), u.addEventListener("mousemove", function (t) { 1 == f && (W = t.pageX, G = t.pageY, W -= u.offsetLeft, G -= u.offsetTop, s.clearRect(0, 0, u.width, u.height), x(), y()) }, !1), u.addEventListener("mouseup", function (t) { f = 0, W = m, G = p, s.clearRect(0, 0, u.width, u.height), x(), y() }, !1)), x(), y(), this.GetWidth = function () { return u.width }, this.GetHeight = function () { return u.height }, this.GetPosX = function () { return W }, this.GetPosY = function () { return G }, this.GetX = function () { return ((W - m) / w * 100).toFixed() }, this.GetY = function () { return ((G - p) / w * 100 * -1).toFixed() }, this.GetDir = function () { var t = "", e = W - m, i = G - p; return i >= k && i <= S && (t = "C"), i < k && (t = "N"), i > S && (t = "S"), e < E && ("C" == t ? t = "W" : t += "W"), e > L && ("C" == t ? t = "E" : t += "E"), t } };

        const view = document.getElementById('stream');
        const WS_URL = "ws://" + window.location.host + ":82";
        const ws = new WebSocket(WS_URL);

        ws.onmessage = message => {
            if (message.data instanceof Blob) {
                var urlObject = URL.createObjectURL(message.data);
                view.src = urlObject;
            }
        };


        var lastText, lastSend, sendTimeout;
        // limit sending to one message every 30 ms
        // https://github.com/neonious/lowjs_esp32_examples/blob/master/neonious_one/cellphone_controlled_rc_car/www/index.html
        function send(txt) {
            var now = new Date().getTime();
            if (lastSend === undefined || now - lastSend >= 30) {
                try {
                    ws.send(txt);
                    lastSend = new Date().getTime();
                    return;
                } catch (e) {
                    console.log(e);
                }
            }
            lastText = txt;
            if (!sendTimeout) {
                var ms = lastSend !== undefined ? 30 - (now - lastSend) : 30;
                if (ms < 0)
                    ms = 0;
                sendTimeout = setTimeout(() => {
                    sendTimeout = null;
                    send(lastText);
                }, ms);
            }
        }

    </script>
</head>


<body>

    <div id='outerContainer'>
        <div style="height: 40vh;"></div>
        <div id="joyDiv" style="width:200px;height:200px;margin:auto;"></div>
    </div>
        
    <div id="motorValues">
        <p>Joy X: <span id="joyXValue">0</span></p>
        <p>Joy Y: <span id="joyYValue">0</span></p>
        <p>Motor 1: <span id="motor1Value">0</span></p>
        <p>Motor 2: <span id="motor2Value">0</span></p>
        <p>Motor 3: <span id="motor3Value">0</span></p>
    </div>

    <script>
        // Create JoyStick object into the DIV 'joyDiv'
        var joy = new JoyStick('joyDiv');
        var inputPosX = document.getElementById("posizioneX");
        var inputPosY = document.getElementById("posizioneY");
        var direzione = document.getElementById("direzione");
        var fuerzaI = document.getElementById("fuerzaI");
        var fuerzaD = document.getElementById("fuerzaD");
        var x = document.getElementById("X");
        var y = document.getElementById("Y");

        function mapValue(value, min1, max1, min2, max2) {
            return (value - min1) * (max2 - min2) / (max1 - min1) + min2;
        }

        function getfuerza(nJoyX, nJoyY) {
            var w_value = 0;
            if (w_value == 1) {
                return 150 + "," + 150 + "," + 150;
            } else if(w_value == -1) {
                return -150 + "," + -150 + "," + -150;
            }
        var motor_cal1 = (-0.333*nJoyX)+(-0.577*nJoyY)+(0.333*w_value);
        var motor_cal2 = (-0.333*nJoyX)+(0.577*nJoyY)+(0.333*w_value);
        var motor_cal3 = (0.666*nJoyX)+(0*nJoyY)+(0.333*w_value);  
        
        var motor_s1 =  mapValue(motor_cal1,-92,92,-255,255);
        var motor_s2 =  mapValue(motor_cal2,-92,92,-255,255);
        var motor_s3 =  mapValue(motor_cal3,-67,67,-255,255);
            
            // Update the motor values on the screen
            document.getElementById("motor1Value").innerText = motor_s1;
            document.getElementById("motor2Value").innerText = motor_s2;
            document.getElementById("motor3Value").innerText = motor_s3;

            // Update Joy X and Joy Y values on the screen
            document.getElementById("joyXValue").innerText = nJoyX;
            document.getElementById("joyYValue").innerText = nJoyY;

            return Math.round(motor_s1) + "," + Math.round(motor_s2) + "," + Math.round(motor_s3);
        }

        setInterval(function () { send(getfuerza(joy.GetX(), joy.GetY())); }, 300);
    </script>

</body>

</html>
```

In this blog, we've covered the complete code behind running PRIMUS. Over the course of these three blogs, I believe I've provided enough detail for anyone to attempt building their own version of the project. As a side note, I've also migrated the entire project to a Raspberry Pi, adding a camera with pan-tilt control for the robot. However, I’m not planning to cover its code in a blog just yet. I've included a photo of the Raspberry-pi PRIMUS.
 *P.S: Horrible wiring alert!!*

![Primus migrated toa raspberry-pi](/img/BlogPrimus/images/blog3/primus_raspi_Blog3.jpeg)

Looking ahead, I plan to release a new series on communication protocols using the ATmega328P. Alternatively, I’m considering starting with an active project that focuses more on embedded software, like a task scheduler.

Until then—stay creative, stay curious!
