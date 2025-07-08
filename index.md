# RFID Lockbox
For my starter project, I chose the Retro Gaming Console. This device recreates the gaming console from a few decades ago, including games like Tetris, Snake.io, Plane Racing, Space Defender and The Number Game.
For my intensive prohject, I chose the RFID Lockbox. This device aims to teach you how an simple security program functions, while also providing you with something practical.

<!---You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:-->
<!---HTML-->
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Jijia L. | Seven Lakes High School | Enviromental Engineering | Incoming Senior

<!---**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**-->

![Headshot](JijiaL.jpg)

# Starter Project

<iframe width="560" height="315" src="https://www.youtube.com/embed/q2iCdOoT5WA?si=4jDJQshDIgnvTNb1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

# Explanation

For my starter project, I chose the Retro Gaming Console. This device recreates the gaming console from a few decades ago, including games like Tetris, Snake.io, Plane Racing, Space Defender and a Slot Machine. Each game utilizes the inputs from the D-Pad style buttons for diffrent functions, as well as the green and yellow button for confirming and pausing/quitting the game. The music changes depends on what game was played, as well as what was displayed on the 7-segment display. The whole device is powered by 3 AAA batteries, or it could be powered via USB-B connection. There's also some options to adjust the brightness levels and the volume. 

# How it works

I used a LED Matrix x2, 7 Segment Display, Buzzer, Button x7, Capacitor, Battery holder, AAA Battery x3, Transparent Acrylic Shell x 6, ICM (Intergrated Circuit Processor), and PCB. The ICM processes the inputs from the 7 buttons/switches and outputs to the screen and 7-segment display.
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**



For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**



For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**



For your first milestone, describe what your project is and how you plan to build it. You can include:
- An explanation about the different components of your project and how they will all integrate together
- Technical progress you've made so far
- Challenges you're facing and solving in your future milestones
- What your plan is to complete your project


# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
// The required libraries for the program
#include <SPI.h>
#include <RFID.h>
#include <Servo.h>

// Setup arduino pin definitions 
#define SS_PIN 10 
#define RST_PIN 9
#define SERVO_PIN 3
#define BUZZER_PIN 8

// Initialise the RFID reader
RFID rfid(SS_PIN,RST_PIN);

// Initialise an instance of the Servo class called "lock"
Servo lock;

// serNum is used for reading and checking the ID number
int serNum[5];

//This integer should be the code of your RFID card / tag 
int cards[][5] = {{182,106,89,165,32}};

bool access = false;
bool boxOpen = true;

int attemptCount = 0;
bool alarmOn = false;

// Function to read the RFID card / tag and determine whether access should be granted
void readCard() {
  if(rfid.readCardSerial()){
      for(int x = 0; x < sizeof(cards); x++){
        for(int i = 0; i < sizeof(rfid.serNum); i++ ){
            if(rfid.serNum[i] != cards[x][i]) {
                access = false;
                break;
            } else {
                access = true;
            }
        }
        if(access) break;
      }
  }
}
void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);
  SPI.begin();
  rfid.init();  
  lock.attach(SERVO_PIN);
  lock.write(5);
}
void loop() {
  // put your main code here, to run repeatedly:
  // The "if" statement only runs if an RFID card / tag is detected
  if(rfid.isCard()){
       /*
        * Check whether the read card serial number matches the saved serial number
        * If the serial number is incorrect, access = false, otherwise access = true
       */
       readCard();
       /*
        * If access is set to true and the box is currently locked, the servo will move to                                    
          unlock the box.
        * If access is set to true and the box is currently unlocked, the servo will move to
          lock the box.
        * If access remains false, a warning buzzer will sound.
        * Each time an attempt is wrong, a count is increased. Once this count reaches 3, an
          alarm will sound.
        * The alarm can only be silenced by using the correct RFID card / tag.
        */
       if(access){
           attemptCount = 0;
           if (!boxOpen) {
              lock.write(5);
              boxOpen = true;
              delay(1000);
           } else {
              lock.write(45);
              boxOpen = false;
              delay(1000);
           }           
       } else {
           attemptCount = attemptCount + 1;
           if (attemptCount < 3) {
              tone(BUZZER_PIN, 330, 500);
              delay(250);
              tone(BUZZER_PIN, 311, 500);
              delay(250);
              noTone(BUZZER_PIN);
           } else {
              alarmOn = true;
              while (alarmOn) {
                tone(BUZZER_PIN, 784, 1000);
                delay(250);
                tone(BUZZER_PIN, 659, 1000);
                delay(250);
                noTone(BUZZER_PIN);                
                if (rfid.isCard()) {
                  readCard(); 
                }                                
                if (access) {
                    attemptCount = 0;
                    alarmOn = false;
                }
              }
           }
       }
  }
  rfid.halt();
}
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
