# SmArTwAtCh
#include <Adafruit_NeoPixel.h>
#include "tinysnore.h"
#define PIN 0
#define NUMPIXELS 6

#define DELAYVAL 500

#include <Adafruit_NeoPixel.h>

int mode = -1;
int numPixel= 0;
boolean isAM = true;

Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);
Adafruit_NeoPixel strip(24, 0, NEO_GRB + NEO_KHZ800);

#include <Bounce2.h>
int buttonPins[4] = {1, 2, 3, 4};
Bounce * buttons = new Bounce[4];

int curHours = 0;
int curMinutes = 0;
int curSeconds = 0;

void setup() {
  initButtons();
  initNeopixels();
}

void loop() {
  updateTime();
  updateButtons();
  
  switch(mode){
    case -1: fallAsleep(); break;
    case 0: dayTime(); break;
    case 1: flashlight(); break;
    case 2: letsParty(); break;
    case 3: allNeopixels(210); break;
  }
}

void dayTime(){
  strip.clear();

  if (isAM == true){
    for ( int i=0; i <= strip.numPixels(); i++){
    strip.setPixelColor(i,200,0,0);
  }
  }
  else {
    for ( int i=0; i <= strip.numPixels(); i++){
    strip.setPixelColor(i,0,0,200);
    }
  }
  strip.show();
}


void updateTime(){
  
curSeconds = millis() /1000;

  if(curSeconds %  60 == 0){
    curMinutes ++;
  }
  
  if(curMinutes %  60 == 0){
    curHours ++;
  }
  
  curHours = curHours % 24;

  if(curHours % 12 == 0){
    isAM = !isAM;
    
  }
  }
  void initButtons(){
  for (int i = 0; i < 4; i++) {
    buttons[i].attach(buttonPins[i], INPUT);
    buttons[i].interval(25);
  }
}

void updateButtons() {
  for (int i = 0; i < 4; i++)  {
    buttons[i].update();
    if (buttons[i].rose()){
      mode = i;
    }
  }
}
void flashlight(){
 strip.clear();
  
  for(int i=0; i<NUMPIXELS; i++) {
    pixels.setPixelColor(i, pixels.Color(250, 250, 250));
    pixels.show();
    delay(DELAYVAL);
  }
   strip.show();
}
long sleepTimer = 0;
long sleepInterval = 5000;

void fallAsleep(){
  if(millis() - sleepTimer > sleepInterval){
    strip.clear(); strip.show();
    snore(5000);
    sleepTimer = millis();
  }
}
void initNeopixels(){
  strip.begin();
  strip.clear();
  strip.show();
  strip.setBrightness(15);
}

void allNeopixels(int c){
  for (int i = 0; i < strip.numPixels(); i++){
    strip.setPixelColor(i, Wheel(c));
  }
  strip.show();
  mode = -1;
}

uint32_t Wheel(byte WheelPos) {
  WheelPos = 255 - WheelPos;
  if(WheelPos < 85) {
    return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);
  }
  if(WheelPos < 170) {
    WheelPos -= 85;
    return strip.Color(0, WheelPos * 3, 255 - WheelPos * 3);
  }
  WheelPos -= 170;
  return strip.Color(WheelPos * 3, 255 - WheelPos * 3, 0);
}

void letsParty(){
  makeColor(strip.Color(130,130,130),50);
  makeColor(strip.Color(130,0,0),50);
  makeColor(strip.Color(0,0,130), 50);
  NEOO(strip.Color(255,0,0),50);
  NEOO(strip.Color(0,255,0),50);
  NEOO(strip.Color(0,0,255),50);
  rainbow(10);
  rainbowGo(50);
}

void NEOO(uint32_t color,int wait) {
  for(int i=0; i<strip.numPixels(); i++) {
    strip.setPixelColor(i, color);
    strip.show();
    delay(wait);
  }
}

void makeColor(uint32_t color,int wait){
  for(int a=0; a<10; a++) {
    for(int b=0; b<3; b++) {
      strip.clear();
      for(int c=b; c<strip.numPixels(); c += 3) {
        strip.setPixelColor(c,color);
      }
      strip.show();
      delay(wait);
    }
  }
}

void rainbow(int wait) {
  for(long firstPixelHue = 0; firstPixelHue < 5*65536; firstPixelHue += 256) {
    for(int i=0; i<strip.numPixels(); i++) {
      int pixelHue = firstPixelHue + (i * 65536L / strip.numPixels());
      strip.setPixelColor(i, strip.gamma32(strip.ColorHSV(pixelHue)));
    }
    strip.show();
    delay(wait);
  }
}

void rainbowGo(int wait) {
  int firstPixelHue = 0;
  for(int a=0; a<30; a++) {
    for(int b=0; b<3; b++) {
      strip.clear();
      for(int c=b; c<strip.numPixels(); c += 3){
        int hue = firstPixelHue + c * 65536L / strip.numPixels();
        uint32_t color = strip.gamma32(strip.ColorHSV(hue));
        strip.setPixelColor(c,color);
      }
      strip.show();
      delay(wait);
      firstPixelHue += 65536 / 90;
    }
  }
}
