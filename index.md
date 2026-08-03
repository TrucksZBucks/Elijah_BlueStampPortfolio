# BlueStamp Floor Cleaning Robot
This project involves a Arduino-controlled self-driving floor-cleaning robot. It uses an ultrasonic sensor and two obstacle avoidance modules to steer out of the way of obstructions. As an extra modification I added remote-controlled LED lights for decoration of the car, which turned out very nice. 


| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Elijah H | Lynbrook High School | Electrical Engineering | Incoming Sophomore

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/GV_1YKm9ogM?si=lawXUwDATfM29vhs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My second milestone was the completion of the base project and establishing a good foundation for further modifications. First, I came up with the idea of screwing two mending plates to the front of the car and securing the desktop vacuum in between the two metal pieces. However, I was met with the challenge of trying to balance the weight of the car and not to make it too front-heavy. Eventually, I thought of moving the vacuum and the mending plates to the back of the car, where there was another wheel that prevented the chassis from tipping over. This idea did the trick and I moved on to attaching the ring lights. After this milestone, I am ready to start coding a website that can manually control the LED lights as my final modification. 

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/PQ8vPVpIn3M?si=1lPLIXNIFWSRnefL" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

My first milestone involved assembling the chassis of the car, testing out the sensors, and coding the basic movement. I first built the chassis of the car and attached the motors. I then wired up the car's battery which allowed for it to drive around. I attached the ultrasonic sensor and obstacle avoidance modules to facilitate its self-driving capabilities. The motors and sensors are all working well through numerous tests of running different code so I am confident that I can move on to the next step. 

# Schematics 
![Schematics Image](Screenshot 2026-07-30 122843.png)


# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
#include <WiFiS3.h>
#include <Adafruit_NeoPixel.h>

char ssid[] = "JIBE";
char pass[] = "shinzai01";

WiFiServer server(80);

#define LED_PIN1 12
#define LED_PIN2 13
#define NUM_LEDS 24

Adafruit_NeoPixel ring1(NUM_LEDS, LED_PIN1, NEO_GRB + NEO_KHZ800);
Adafruit_NeoPixel ring2(NUM_LEDS, LED_PIN2, NEO_GRB + NEO_KHZ800);

// Movement code 
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;
const int echoPin = 4;
const int trigPin = 3;
const int rightIR = 7;
const int leftIR = 8;

float readSensorData() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  long duration = pulseIn(echoPin, HIGH, 30000);
  if(duration==0) return 999;
  return duration/58.0;
}

void moveForward(int speed){
  speed=constrain(speed,0,255);
  analogWrite(A_1B,0); analogWrite(A_1A,speed);
  analogWrite(B_1B,speed); analogWrite(B_1A,0);
}
void moveBackward(int speed){
  speed=constrain(speed,0,255);
  analogWrite(A_1B,speed); analogWrite(A_1A,0);
  analogWrite(B_1B,0); analogWrite(B_1A,speed);
}
void backLeft(int speed){
  speed=constrain(speed,0,255);
  analogWrite(A_1B,speed/2); analogWrite(A_1A,0);
  analogWrite(B_1B,0); analogWrite(B_1A,speed);
}
void backRight(int speed){
  speed=constrain(speed,0,255);
  analogWrite(A_1B,speed); analogWrite(A_1A,0);
  analogWrite(B_1B,0); analogWrite(B_1A,speed/2);
}

void setColor(uint8_t r,uint8_t g,uint8_t b){
  for(int i=0;i<NUM_LEDS;i++){
    ring1.setPixelColor(i, ring1.Color(r,g,b));
    ring2.setPixelColor(i, ring2.Color(r,g,b));
  }
  ring1.show();
  ring2.show();
}

// code for red, blue, and green settings for lights
void rotatingColor(uint8_t r, uint8_t g, uint8_t b){
  static int offset = 0;

  for(int i = 0; i < NUM_LEDS; i++){

    // distance from moving LED
    int distance = abs(i - offset);

    // account for circular wrap
    if(distance > NUM_LEDS / 2){
      distance = NUM_LEDS - distance;
    }

    uint8_t brightness;

    // bright center with fading tail
    if(distance == 0){
      brightness = 255;
    }
    else if(distance == 1){
      brightness = 180;
    }
    else if(distance == 2){
      brightness = 80;
    }
    else{
      brightness = 10;
    }

    ring1.setPixelColor(i,
      ring1.Color(
        (r * brightness) / 255,
        (g * brightness) / 255,
        (b * brightness) / 255
      )
    );

    ring2.setPixelColor(i,
      ring2.Color(
        (r * brightness) / 255,
        (g * brightness) / 255,
        (b * brightness) / 255
      )
    );
  }

  ring1.show();
  ring2.show();

  offset++;
  if(offset >= NUM_LEDS) offset = 0;
}

// rainbow mode
void rainbowStep(){
  static uint16_t j=0;
  for(int i=0;i<NUM_LEDS;i++){
    uint32_t c=ring1.gamma32(ring1.ColorHSV((i*65536L/NUM_LEDS+j)&65535));
    ring1.setPixelColor(i,c);
    ring2.setPixelColor(i,c);
  }
  ring1.show(); ring2.show();
  j+=256;
}

enum Mode {OFF,RED,GREEN,BLUE,RAINBOW};
Mode mode=OFF;

const char PAGE[] = R"rawliteral(
<!DOCTYPE html><html><body style="font-family:Arial;text-align:center">
<h2>CleanBot LEDs</h2>
<button onclick="fetch('/red')">Red</button>
<button onclick="fetch('/green')">Green</button>
<button onclick="fetch('/blue')">Blue</button>
<button onclick="fetch('/rainbow')">Rainbow</button>
<button onclick="fetch('/off')">Off</button>
</body></html>)rawliteral";

void handleClient(){
  WiFiClient client=server.available();
  if(!client) return;
  String req=client.readStringUntil('\r');
  client.flush();

  if(req.indexOf("GET /red")>=0) mode=RED;
  else if(req.indexOf("GET /green")>=0) mode=GREEN;
  else if(req.indexOf("GET /blue")>=0) mode=BLUE;
  else if(req.indexOf("GET /rainbow")>=0) mode=RAINBOW;
  else if(req.indexOf("GET /off")>=0) mode=OFF;

  client.println("HTTP/1.1 200 OK");
  client.println("Content-Type: text/html");
  client.println("Connection: close");
  client.println();
  client.print(PAGE);
  client.stop();
}

void setup(){
  Serial.begin(9600);
  pinMode(A_1A,OUTPUT); pinMode(A_1B,OUTPUT);
  pinMode(B_1A,OUTPUT); pinMode(B_1B,OUTPUT);
  pinMode(trigPin,OUTPUT); pinMode(echoPin,INPUT);
  pinMode(leftIR,INPUT); pinMode(rightIR,INPUT);

  ring1.begin(); ring2.begin();
  ring1.setBrightness(25);
  ring2.setBrightness(25);
  ring1.show(); ring2.show();

  //connect to Wifi
  while(WiFi.begin(ssid,pass)!=WL_CONNECTED){ delay(3000); }
  server.begin();
  Serial.println(WiFi.localIP());
}

void loop(){
  handleClient();

  switch(mode){
    case RED:rotatingColor(255,0,0);break;
    case GREEN:rotatingColor(0,255,0);break;
    case BLUE:rotatingColor(0,0,255);break;
    case OFF:setColor(0,0,0);break;
    case RAINBOW:rainbowStep();break;
  }

  int left=digitalRead(leftIR);
  int right=digitalRead(rightIR);

  if(!left && right) backLeft(180);
  else if(left && !right) backRight(180);
  else if(!left && !right) moveBackward(180);
  else{
    float distance=readSensorData();
    if(distance>50) moveForward(200);
    else if(distance>2 && distance<10){
      moveBackward(200);
      delay(1000);
      backLeft(180);
      delay(500);
    } else moveForward(150);
  }

  if(mode==RAINBOW) delay(2);
}
```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Sunfounder Kit | Serves as the base of the floor cleaning robot | $62.99 | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/](https://www.amazon.com/SunFounder-Compatible-Tutorials-Including-Controller/dp/B0B778L1DZ/"> Link </a> |
| Double Sided Foam Tape | Sticks items together | $8.09 | <a href="https://www.amazon.com/Mounting-Waterproof-Adhesive-Posters-Sensors/dp/B0DJLR75H9/"> Link </a> |
| Mini desktop vacuum | Cleans the floor | $12.98 | <a href="https://www.amazon.com/dp/B07Q128V6W"> Link </a> |
| 9V Batteries | Powers the robot | $8.88 | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/"> Link </a> |
| DMM | Measures electrical signals | $9.99 | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/](https://www.amazon.com/dp/B0CXM242J1"> Link </a> |
| AA Batteries | Powers the robot | $9.99 | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/](https://www.amazon.com/dp/B0CXM242J1](https://www.amazon.com/Amazon-Basics-Batteries-Leak-Free-Household/dp/B00NTCH52W/"> Link </a> |
| 24 Bits WS2812 LED Ring | Decorative Lights | $8.99 | <a href="https://www.amazon.com/Sparkleiot-Integrated-Drivers-Arduino-Raspberry/dp/B09K58DMMX/"> Link </a> |
| Arduino Uno R4 WiFi | Microcontroller Board | $19.99 | <a href="https://www.amazon.com/DIYables-Development-Compatible-Bluetooth-Prototyping/dp/B0FRZY4YRT/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Base Project Manual](https://docs.sunfounder.com/projects/3in1-kit-v2/en/latest/car_project/car_project.html)
- [Saagnik's Floor Cleaning Robot Portfolio](https://smitra123.github.io/Saagnik-Mitra-s-BSE-Portfolio)

To watch the BSE tutorial on how to create a portfolio, click here.
