# RFID Lockbox
My intensive project is an Arduino-based, two-card RFID access controller that only unlocks when you present two distinct, authorized keycards in sequence. A MG995 servo motor physically retracts the lock cam by 90° CCW, then returns to its horizontal “rest” position. A 16×2 I²C LCD guides the user through each step (“Scan Card #1,” “Card 2 Invalid,” “Access Granted”), while an active buzzer and red/green LEDs deliver audible and visual feedback for success or failure. All electronics—the MFRC522 reader, servo, display, buzzer and LEDs—share a common 5 V supply and ground, making the system reliable both on USB power and standalone battery.

<!---You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:-->
<!---HTML-->
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Jijia L. | Seven Lakes High School | Enviromental Engineering | Incoming Senior

<!---**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**-->

<img src="JijiaL.jpg" alt="Alt Text" style="width:50%; height:auto;">

# Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/q2iCdOoT5WA?si=4jDJQshDIgnvTNb1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Explanation

For my starter project, I chose the Retro Gaming Console. This device recreates the gaming console from a few decades ago, including games like Tetris, Snake.io, Plane Racing, Space Defender and a Slot Machine. Each game utilizes the inputs from the D-Pad style buttons for diffrent functions, as well as the green and yellow button for confirming and pausing/quitting the game. The music changes depends on what game was played, as well as what was displayed on the 7-segment display. The whole device is powered by 3 AAA batteries, or it could be powered via USB-B connection. There's also some options to adjust the brightness levels and the volume.<br><br>During the process of constructing the console, the biggest challenge for me was to not accidentally solder two joints toghether, as that could create a short between them. But because I've soldered a lot before, this wasn't a huge issue for me, I just slowed down and was more careful, and the soldering turned out great with everything working as it should. But the real issue that prevented me from completing the project easily was the unclear instruction. The phamplet included in my kit are missing some of the crutial steps like where to solder the wires to, which direction each conponent should face, and what types of wiring should you use. In the end, I figured out how each will work toghether and where they would go by looking up info online, and asking my friends for advices.

# How it works

I used a LED Matrix x2, 7 Segment Display, Buzzer, Button x7, Capacitor, Battery holder, AAA Battery x3, Transparent Acrylic Shell x 6, ICM (Intergrated Circuit Processor), and PCB. The ICM processes the inputs from the 7 buttons/switches and outputs to the screen and 7-segment display.

# First Milestone
For my first milestone, I completed the construction and wiring of the core components that make up the locking function of the project. I mounted an Arduino UNO R3 (with its servo‐driver extension shield) in its enclosure and soldered the header for the MG995 servo onto the board. I then ran a dedicated 5 V supply line—complete with a 470 μF decoupling capacitor—to the servo’s red and brown leads, and connected its control wire to digital pin 6. With this hardware in place, I was able to upload a simple sweep sketch that reliably moves the servo arm between its horizontal “rest” position and the 90° CCW “active” position needed to retract the lock.

On the software side, I have verified that the MG995 will consistently hit both its start-and-end angles when powered from both USB and battery sources. To eliminate voltage sag during motion, I tested the system on a USB power bank and observed stable performance once the capacitor was in place. I also established a common ground between the Arduino and the external supply, ensuring the servo and controller share a reference. These early tests give me confidence that the locking mechanism will operate smoothly under standalone battery power.

Looking ahead, the primary challenges will be integrating the MFRC522 RFID reader and implementing the two-card authentication logic without mechanical interference. I will need to calibrate the servo angles in situ—tweaking the MOTOR_REST_ANGLE and MOTOR_ACTIVE_ANGLE constants in code—so that the lock cam aligns precisely with its strike plate. I also need to design a tidy mounting scheme for the RFID antenna inside the enclosure and route all wiring so that nothing catches when the servo moves. Finally, I’ll test reliability over hundreds of cycles to guard against wear or binding.

Looking ahead from this hardware‐focused first milestone, my very next steps are pure firmware work: I will integrate the MFRC522 library and write the dual‐card authentication routine that reads, buffers, and compares two distinct UIDs in sequence; build a state-driven showMessage() API that drives the I²C LCD and echoes states over Serial; implement the LED and buzzer signaling logic so that green LED#1 lights on a valid first card, green LED#2 only after the second, and the red LED and error tones fire on any invalid scan; and tie it all together with clean, nonblocking loops that halt and reinitialize the reader correctly between scans. Completing this coding phase will prove the full access-control flow before I move on to final refinements and enclosure design.
## First Milestone Video

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.*-->
<iframe width="560" height="315" src="https://www.youtube.com/embed/M0pmq5avC0Q?si=Eaqc1bn8x1eadtzN" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

<!---For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project-->

# Second Milestone

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**-->

<!---For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone.-->
For my second milestone, I dedicated my time entirely to fleshing out the core authentication code and user‐feedback routines that ties every component together. First, I integrated the MFRC522 library and wrote a robust readCard() helper that blocks until a tag is present, reads its 4-byte UID into a buffer, and halts the reader cleanly. I then built an isAuthorized() function that compares that buffer against a hard-coded list of allowed UIDs. To ensure the two-card requirement, I added logic to store the first UID, wait until that tag is removed (polling PICC_IsNewCardPresent() until it’s gone), and then loop until a second, distinct authorized UID is presented.

On top of that, I encapsulated all user messaging in a single showMessage() routine that both writes to the I²C LCD and echoes the same text over Serial. This proved invaluable for headless debugging, as I could watch every state transition (“Card 1 OK,” “Please Scan Card #2,” etc.) in parallel with the display. I also implemented the LED and buzzer feedback: the red LED now pulses on any invalid scan, while Green #1 lights immediately after a valid first card and Green #2 comes on only when the second card passes. All of these signals persist exactly until the next state, then clear together when access is finally granted.

One surprise came from handling the reader’s “card still present” state: without the removal loop, the reader would stubbornly keep detecting the same first card and never advance to the second step. Resolving that took careful testing of PICC_IsNewCardPresent() and small delays to avoid rapid re-triggers. I also wrestled with library quirks—like needing to call PCD_Init() and PICC_HaltA() in just the right places to prevent lock-ups—and ironed out several off-by-one bugs in the UID comparison loops.

For the final milestone, I still need to polish the code by refactoring common patterns into reusable functions (for example, abstracting the dual-card flow into a single state machine), implement non-blocking timing with millis() so the loop remains responsive, and add configurable storage (perhaps EEPROM) for the authorized UIDs. I’ll also build in better error handling for edge cases—such as power-on resets with a card already in the field—and prepare full documentation of the code flow so the system can be maintained and extended easily.

## Second Milestone Video
<iframe width="560" height="315" src="https://www.youtube.com/embed/fDCb2J9xpOk?si=-7EMGv_uoDvhrFOk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Final Milestone

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**-->

<!---For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE-->

# Schematics 
<!---Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/)-->

# Code
```c++
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SPI.h>
#include <MFRC522.h>
#include <Servo.h>

// —— Pins ——
#define SS_PIN         10   // RFID SS
#define RST_PIN         9   // RFID RST
#define LOCK_PIN        3   // locking servo
#define MOTOR_PIN       6   // MG995 servo signal
#define BUZZER_PIN      8   // active buzzer
#define LED_RED_PIN     7   // red error LED
#define LED_GREEN1_PIN  2   // green #1 (card1 OK)
#define LED_GREEN2_PIN  4   // green #2 (card2 OK)

// —— Motor angles & timing ——  
const uint8_t   MOTOR_REST_ANGLE   = 90;    // horizontal “rest” (servo centered)
const uint8_t   MOTOR_ACTIVE_ANGLE = 180;   // +90° CCW from rest
const unsigned long MOTOR_HOLD_MS  = 2000;  // hold at active angle (ms)

// —— I²C LCD ——
LiquidCrystal_I2C lcd(0x27, 16, 2);

// —— RFID & servos ——
MFRC522 rfid(SS_PIN, RST_PIN);
Servo    lockServo, motorServo;

// —— Authorized UIDs ——  
byte cards[][4] = {
  { 138,  56,   8,   5 },   // Card #1
  { 160, 170,  22,   8 }    // Card #2
};

void setup() {
  Serial.begin(9600);
  SPI.begin();
  rfid.PCD_Init();

  // lock servo
  lockServo.attach(LOCK_PIN);
  lockServo.write(5);                // locked position

  // motor servo
  motorServo.attach(MOTOR_PIN);
  motorServo.write(MOTOR_REST_ANGLE); // start horizontal

  // buzzer
  pinMode(BUZZER_PIN, OUTPUT);
  digitalWrite(BUZZER_PIN, LOW);

  // LEDs
  pinMode(LED_RED_PIN,    OUTPUT);
  pinMode(LED_GREEN1_PIN, OUTPUT);
  pinMode(LED_GREEN2_PIN, OUTPUT);
  digitalWrite(LED_RED_PIN,    LOW);
  digitalWrite(LED_GREEN1_PIN, LOW);
  digitalWrite(LED_GREEN2_PIN, LOW);

  // LCD
  lcd.init();
  lcd.backlight();
  showMessage("Please Scan", "Card #1");
}

void loop() {
  byte uid1[4], uid2[4];

  // —— Card #1 ——
  if (!readCard(uid1)) return;
  if (!isAuthorized(uid1)) {
    indicateWrong();
    deny("Card 1 Invalid");
    showMessage("Please Scan", "Card #1");
    return;
  }
  digitalWrite(LED_RED_PIN,    LOW);
  digitalWrite(LED_GREEN1_PIN, HIGH);
  digitalWrite(LED_GREEN2_PIN, LOW);

  // wait for removal
  showMessage("Card 1 OK", "Remove Card");
  while (rfid.PICC_IsNewCardPresent()) delay(50);
  delay(200);

  // —— Card #2 ——
  while (true) {
    showMessage("Please Scan", "Card #2");
    if (!readCard(uid2)) continue;

    if (!isAuthorized(uid2)) {
      indicateWrong();
      deny("Card 2 Invalid");
      continue;
    }
    if (memcmp(uid1, uid2, 4) == 0) {
      indicateWrong();
      deny("Same Card");
      continue;
    }
    digitalWrite(LED_RED_PIN,    LOW);
    digitalWrite(LED_GREEN1_PIN, HIGH);
    digitalWrite(LED_GREEN2_PIN, HIGH);
    break;
  }

  // —— Grant access ——
  grantAccess();
}

// — Helpers —

// Blocks until a tag is presented & read; fills buf[4]
bool readCard(byte buf[4]) {
  while (!rfid.PICC_IsNewCardPresent());
  if (!rfid.PICC_ReadCardSerial()) return false;
  for (byte i = 0; i < 4; i++) buf[i] = rfid.uid.uidByte[i];
  rfid.PICC_HaltA();
  return true;
}

// Returns true if uid[4] matches one of your cards
bool isAuthorized(byte uid[4]) {
  for (size_t x = 0; x < sizeof(cards)/sizeof(cards[0]); x++) {
    bool ok = true;
    for (byte i = 0; i < 4; i++) {
      if (cards[x][i] != uid[i]) { ok = false; break; }
    }
    if (ok) return true;
  }
  return false;
}

// Write two lines to the LCD and echo over Serial
void showMessage(const char* l1, const char* l2) {
  lcd.clear();
  lcd.setCursor(0,0); lcd.print(l1);
  lcd.setCursor(0,1); lcd.print(l2);
  Serial.print(l1);
  if (*l2) {
    Serial.print("  |  ");
    Serial.print(l2);
  }
  Serial.println();
}

// Turn red LED on, greens off
void indicateWrong() {
  digitalWrite(LED_RED_PIN,    HIGH);
  digitalWrite(LED_GREEN1_PIN, LOW);
  digitalWrite(LED_GREEN2_PIN, LOW);
}

// Show msg, beep twice
void deny(const char* msg) {
  showMessage(msg, "");
  for (int i = 0; i < 2; i++) {
    digitalWrite(BUZZER_PIN, HIGH);
    delay(100);
    digitalWrite(BUZZER_PIN, LOW);
    delay(100);
  }
  delay(800);
}

// Unlock + motor sweep CCW 90° + reset LEDs
void grantAccess() {
  showMessage("Access", "Granted");
  digitalWrite(BUZZER_PIN, HIGH);
  delay(300);
  digitalWrite(BUZZER_PIN, LOW);

  // unlock
  lockServo.write(90);
  delay(500);

  // rotate motor CCW 90° from horizontal
  motorServo.write(MOTOR_ACTIVE_ANGLE);
  delay(MOTOR_HOLD_MS);
  // return to horizontal
  motorServo.write(MOTOR_REST_ANGLE);
  delay(500);

  // lock back
  lockServo.write(5);
  delay(500);

  // turn LEDs off
  digitalWrite(LED_RED_PIN,    LOW);
  digitalWrite(LED_GREEN1_PIN, LOW);
  digitalWrite(LED_GREEN2_PIN, LOW);

  delay(1000);
  showMessage("Please Scan", "Card #1");
}
```

# Bill of Materials
<!---Here's where you'll list the parts in your project. To add more rows, just copy and paste the example rows below.
Don't forget to place the link of where to buy each component inside the quotation marks in the corresponding row after href =. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize this to your project needs. -->

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| ELEGOO UNO R3 Project Super Starter Kit| The core conponents for the project | $35.99 | <a href="https://a.co/d/9hBT6cX"> Link </a> |
| SunFounder Reader Module Kit Mifare RC522 Reader Module | An Arduino-compataibile RFID module | $8.99 | <a href="https://a.co/d/eX8J4wL"> Link </a> |
| YAMASO 20PCS L Bracket Corner Bracket with 60PCS Screws | Creating the structure and fastening the pieces toghether | $5.99 | <a href="https://a.co/d/65ODQW2"> Link </a> |
| MMOBIEL Micro Servo Motor Kit MG995 55g 90° | Locking mechanism for the project | $8.49 | <a href="https://a.co/d/dIDshdN"> Link </a> |
| 5 AA Battery Holder with Wires | Provides power for the system | $7.99 | <a href="https://a.co/d/0rVpuwI"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)
