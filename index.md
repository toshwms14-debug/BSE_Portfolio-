# Project-Robertson Or The Arduino Uno R3


Hi, I'm Tosh Williams Patterson, and this is my student portfolio 
This is about my project, the Arduino Uno R3 but I like to call it Robertson, for No Exact Reason 

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Tosh W.P | going to Pemmott Middle | Electrical Engineering, Music, Coding, Baking, And Drawing, Eta | Grade 6th (technically in 5th and about to be in 6th) 


![Headstone Image](logo.svg)

# The Demo Night Milestone
<iframe width="560" height="315" src="https://www.youtube.com/embed/wIUgYopDJWs?si=ArItXB2jv585P0Ip" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

I Hope That the Future Students Here at Bluestamps Make Great Things and Will Always Work Hard and Never Giving Up and Persevere Too, I thank Everyone Who Helped Me to This Point and Will Never Forget This Experience Here, So I Am Proud Of My porject, Robertson.    


# Final Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone the code is now able to enter it now but i removed the camera module and had no time to re-add it 
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/QfTfmKrVslU?si=ZRfOIouY_PULFxvX" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

-  the Arduino is not letting the code in for some reason 
-  the camera module is to improve its navigation and obstacle avoiding sensor 
-The problem I faced was not getting the wires mixed up and confusing one wire for another
- I'm Finishing the camera module code, and I think it's about 95% complete 

# First Milestone 
<iframe width="560" height="315" src="https://www.youtube.com/embed/i5-jHNgmA0U?si=6OiJg2PzBiqLW92b" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>


- the problem I faced was not getting the wires mixed up and getting confused with them
- The Plan is to make it more focused on the self-driving part of it
  3
# Starter Project: The R.G.B Slider
<iframe width="560" height="315" src="https://www.youtube.com/embed/8o4nzQ7FKmY?si=4_sG5iXU93aqPhAx" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
-
# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code 
```cpp
const int A_1B = 5;
const int A_1A = 6;
const int B_1B = 9;
const int B_1A = 10;

const int echoPin = 4;
const int trigPin = 3;
const int rightIR = 7;
const int leftIR = 8;

const int LED_FRONT_L = 11;
const int LED_FRONT_R = 12;
const int LED_REAR_L = 2;
const int LED_REAR_R = 13;

// Speed zones
const int SPD_STOP = 0;
const int SPD_CRAWL = 100;
const int SPD_SLOW = 150;
const int SPD_NORMAL = 180;
const int SPD_FAST = 220;

// Timings
const unsigned long BACK_TIME = 900;
const unsigned long TURN_TIME = 600;
const unsigned long BLINK_INTERVAL = 150;
const unsigned long SENSOR_INTERVAL = 60;
const int RAMP_STEP = 8;
const unsigned long RAMP_DELAY = 12;
const unsigned long STUCK_LIMIT = 2500;

// State machine
enum State { NORMAL,
             BACKING,
             TURNING_LEFT,
             TURNING_RIGHT,
             STUCK };
State carState = NORMAL;
unsigned long stateStart = 0;
unsigned long lastSensor = 0;
unsigned long lastBlink = 0;
unsigned long backingStart = 0;
bool blinkOn = false;
int turnDir = 0;  // -1=left, 1=right, toggles on stuck
int currentSpeed = 0;
float lastDist = 999;

// --- LED helpers ---
void ledsOff() {
  digitalWrite(LED_FRONT_L, LOW);
  digitalWrite(LED_FRONT_R, LOW);
  digitalWrite(LED_REAR_L, LOW);
  digitalWrite(LED_REAR_R, LOW);
}
void frontLeds() {
  digitalWrite(LED_FRONT_L, HIGH);
  digitalWrite(LED_FRONT_R, HIGH);
  digitalWrite(LED_REAR_L, LOW);
  digitalWrite(LED_REAR_R, LOW);
}
void rearLeds() {
  digitalWrite(LED_FRONT_L, LOW);
  digitalWrite(LED_FRONT_R, LOW);
  digitalWrite(LED_REAR_L, HIGH);
  digitalWrite(LED_REAR_R, HIGH);
}
void blinkLeft() {
  bool b = blinkOn;
  digitalWrite(LED_FRONT_L, b);
  digitalWrite(LED_REAR_L, b);
  digitalWrite(LED_FRONT_R, LOW);
  digitalWrite(LED_REAR_R, LOW);
}
void blinkRight() {
  bool b = blinkOn;
  digitalWrite(LED_FRONT_R, b);
  digitalWrite(LED_REAR_R, b);
  digitalWrite(LED_FRONT_L, LOW);
  digitalWrite(LED_REAR_L, LOW);
}

// --- Motor helpers ---
void setMotors(int la, int lb, int ra, int rb) {
  analogWrite(A_1A, la);
  analogWrite(A_1B, lb);
  analogWrite(B_1B, ra);
  analogWrite(B_1A, rb);
}
void stopMove() {
  setMotors(0, 0, 0, 0);
}
void driveForward(int spd) {
  setMotors(spd, 0, spd, 0);
}
void driveBackward(int spd) {
  setMotors(0, spd, 0, spd);
}
void pivotLeft(int spd) {
  setMotors(0, spd, spd, 0);
}  // reverse left, forward right
void pivotRight(int spd) {
  setMotors(spd, 0, 0, spd);
}  // forward left, reverse right
void backLeft(int spd) {
  setMotors(0, spd, 0, 0);
}
void backRight(int spd) {
  setMotors(0, 0, 0, spd);
}

// --- Speed ramp ---
void rampTo(int target, bool forward) {
  if (currentSpeed < target) {
    currentSpeed += RAMP_STEP;
    if (currentSpeed > target) currentSpeed = target;
  } else if (currentSpeed > target) {
    currentSpeed -= RAMP_STEP;
    if (currentSpeed < target) currentSpeed = target;
  }
  if (forward) driveForward(currentSpeed);
  else driveBackward(currentSpeed);
}

// --- Ultrasonic ---
float readDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  long dur = pulseIn(echoPin, HIGH, 25000);  // 25ms timeout ~4m
  if (dur == 0) return 400.0;
  return dur / 58.0;
}

// --- State transitions ---
void enterBacking() {
  carState = BACKING;
  stateStart = millis();
  backingStart = stateStart;
  driveBackward(SPD_SLOW);
  rearLeds();
  Serial.println(">> BACKING");
}

void enterTurn() {
  unsigned long now = millis();
  if (now - backingStart > STUCK_LIMIT) {
    carState = STUCK;
    turnDir = -turnDir;  // flip turn direction if stuck
    Serial.println(">> STUCK: flipping turn");
  } else {
    carState = (turnDir >= 0) ? TURNING_LEFT : TURNING_RIGHT;
  }
  stateStart = now;
  currentSpeed = 0;
  Serial.print(">> TURNING ");
  Serial.println((turnDir >= 0) ? "LEFT" : "RIGHT");
}

void setup() {
  Serial.begin(9600);
  pinMode(A_1B, OUTPUT);
  pinMode(A_1A, OUTPUT);
  pinMode(B_1B, OUTPUT);
  pinMode(B_1A, OUTPUT);
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  pinMode(leftIR, INPUT);
  pinMode(rightIR, INPUT);
  pinMode(LED_FRONT_L, OUTPUT);
  pinMode(LED_FRONT_R, OUTPUT);
  pinMode(LED_REAR_L, OUTPUT);
  pinMode(LED_REAR_R, OUTPUT);
  ledsOff();
  turnDir = 1;  // default first turn left
  Serial.println("Car ready.");
}

void loop() {
  unsigned long now = millis();

  // Blink ticker
  if (now - lastBlink >= BLINK_INTERVAL) {
    blinkOn = !blinkOn;
    lastBlink = now;
  }

  // --- State: BACKING ---
  if (carState == BACKING) {
    rearLeds();
    if (now - stateStart >= BACK_TIME) enterTurn();
    return;
  }

  // --- State: TURNING_LEFT ---
  if (carState == TURNING_LEFT) {
    blinkLeft();
    pivotLeft(SPD_SLOW);
    if (now - stateStart >= TURN_TIME) {
      carState = NORMAL;
      currentSpeed = 0;
      ledsOff();
    }
    return;
  }

  // --- State: TURNING_RIGHT ---
  if (carState == TURNING_RIGHT) {
    blinkRight();
    pivotRight(SPD_SLOW);
    if (now - stateStart >= TURN_TIME) {
      carState = NORMAL;
      currentSpeed = 0;
      ledsOff();
    }
    return;
  }

  // --- State: STUCK ---
  if (carState == STUCK) {
    blinkOn ? rearLeds() : ledsOff();  // hazard flash
    if (now - stateStart >= TURN_TIME) {
      carState = NORMAL;
      currentSpeed = 0;
      ledsOff();
    }
    return;
  }

  // --- State: NORMAL ---
  int left = digitalRead(leftIR);    // 0 = obstacle
  int right = digitalRead(rightIR);  // 0 = obstacle
```

# Bill of Materials
Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. 

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
