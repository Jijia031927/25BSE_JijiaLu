# RFID Lockbox
For my starter project, I chose the Retro Gaming Console. This device recreates the gaming console from a few decades ago, including games like Tetris, Snake.io, Plane Racing, Space Defender and The Number Game.
For my intensive project, I chose the RFID Lockbox. This device aims to teach you how an simple security program functions, while also providing you with something practical.

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

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**



For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project-->

# Second Milestone

<!---**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**



For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone.-->

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
#include <Keypad.h>
#include <SPI.h>
#include <MFRC522.h>
#include <Servo.h>

// —— Pins ——
// RFID (unchanged)
#define SS_PIN       10
#define RST_PIN       9
// Servos (unchanged)
#define LOCK_PIN      3
#define MOTOR_PIN     6
// Active buzzer (unchanged)
#define BUZZER_PIN    8

// I²C LCD at 0x27, size 16×2
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Keypad setup (4×3)
const byte ROWS = 4, COLS = 3;
char keys[ROWS][COLS] = {
  { '1','2','3' },
  { '4','5','6' },
  { '7','8','9' },
  { '*','0','#' }
};
byte rowPins[ROWS] = { 2, 4, 5, 7 };   // you can adjust as needed
byte colPins[COLS] = { A0, A1, A2 };
Keypad keypad(makeKeymap(keys), rowPins, colPins, ROWS, COLS);

// RFID & servos & buzzer
MFRC522  rfid(SS_PIN, RST_PIN);
Servo    lockServo, motorServo;

// Authorized cards (replace with your own 4-byte UID)
byte cards[][4] = {
  { 138, 56, 8, 5 }
};

// Your 2FA passcode
const String PASSCODE = "1234";

// How long the MG995 holds its position (ms)
const unsigned long MOTOR_HOLD_MS = 2000;

void setup() {
  Serial.begin(9600);
  SPI.begin();
  rfid.PCD_Init();

  // Locking servo
  lockServo.attach(LOCK_PIN);
  lockServo.write(5);    // “locked” angle

  // Motor servo
  motorServo.attach(MOTOR_PIN);
  motorServo.write(0);   // rest position

  // Active buzzer
  pinMode(BUZZER_PIN, OUTPUT);
  digitalWrite(BUZZER_PIN, LOW);

  // LCD
  lcd.init();
  lcd.backlight();
  showScanCard();
}

void loop() {
  // 1) Wait for a card
  if (!rfid.PICC_IsNewCardPresent()) return;
  if (!rfid.PICC_ReadCardSerial())      return;

  bool cardOk = checkCard();
  rfid.PICC_HaltA();

  if (!cardOk) {
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Card Invalid");
    beepWrong();
    delay(1500);
    showScanCard();
    return;
  }

  // 2) Card OK → ask for passcode
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Please Enter");
  lcd.setCursor(0,1);
  lcd.print("Passcode");

  bool pinOk = enterPasscode();
  if (!pinOk) {
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Wrong Passcode");
    beepWrong();
    delay(1500);
    showScanCard();
    return;
  }

  // 3) Both OK → grant access
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Access Granted");
  beepCorrect();

  // Unlock servo
  lockServo.write(90);
  delay(500);
  // Motor action
  motorServo.write(45);
  delay(MOTOR_HOLD_MS);
  motorServo.write(0);
  delay(500);
  // Lock back
  lockServo.write(5);

  delay(1000);
  showScanCard();
}

// — Helper functions —

void showScanCard() {
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Please Scan");
  lcd.setCursor(0,1);
  lcd.print("Your Card");
}

bool checkCard() {
  for (size_t x = 0; x < sizeof(cards)/sizeof(cards[0]); x++) {
    bool match = true;
    for (byte i = 0; i < 4; i++) {
      if (rfid.uid.uidByte[i] != cards[x][i]) {
        match = false;
        break;
      }
    }
    if (match) return true;
  }
  return false;
}

bool enterPasscode() {
  String input = "";
  lcd.setCursor(0,1);
  while (true) {
    char k = keypad.getKey();
    if (!k) continue;
    if (k >= '0' && k <= '9' && input.length() < PASSCODE.length()) {
      input += k;
      lcd.print('*');
    }
    else if (k == '#') {
      return (input == PASSCODE);
    }
    else if (k == '*') {
      // clear entry
      input = "";
      lcd.setCursor(0,1);
      lcd.print("                ");
      lcd.setCursor(0,1);
    }
  }
}

void beepCorrect() {
  digitalWrite(BUZZER_PIN, HIGH);
  delay(300);
  digitalWrite(BUZZER_PIN, LOW);
}

void beepWrong() {
  for (int i = 0; i < 2; i++) {
    digitalWrite(BUZZER_PIN, HIGH);
    delay(100);
    digitalWrite(BUZZER_PIN, LOW);
    delay(100);
  }
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
