![image](https://github.com/TamarVilner/SimonGame/assets/144704638/64f5edec-00b7-4029-bfd9-4e529135ae37)


#include "pitches.h"
#define SPEAKER_PIN 18
#define MAX_GAME_LENGTH  100

const uint8_t buttonPins [] = {0,1,2,3};
const uint8_t ledPins  [] = {4,5,6,7};
const uint8_t gameTones [] = {NOTE_G3, NOTE_C4, NOTE_E4, NOTE_G5};
uint8_t gameSequence [MAX_GAME_LENGTH] = {0};
uint8_t gameIndex = 0;

void setup() {
 Serial.begin(9600);  
 Serial.println("START Game");
 for(byte i=0; i<4; i++)
 {
  pinMode(ledPins[i], OUTPUT);
  pinMode(buttonPins[i], INPUT_PULLUP);
 }
 pinMode(SPEAKER_PIN, OUTPUT);
 randomSeed(analogRead(8));
}

void loop() {
  gameSequence[gameIndex]= random(0, 4);
  gameIndex = gameIndex + 1;
  if(gameIndex>=MAX_GAME_LENGTH)
      gameIndex = MAX_GAME_LENGTH - 1;
  playSequence(); 
  if(!checkUserSequence ())
      gameOver();
  delay(300);
  if(gameIndex>0)   {
    playLevelUpSound();
    delay(300);
  }
}

void lightLedAndPlayTone(byte indexLed){
digitalWrite(ledPins[indexLed], HIGH);
tone(SPEAKER_PIN, gameTones[indexLed]);
delay(300);
digitalWrite(ledPins[indexLed], LOW);
noTone(SPEAKER_PIN);
}

void playSequence(){
  for(byte i = 0; i<gameIndex; i++){
    lightLedAndPlayTone(gameSequence[i]);
    delay(50);
  }
}

byte readButtons(){
  while(true)
  {
  for(byte i=0; i<4; i++){
    if(digitalRead(buttonPins[i])==0){
      return i;

    }
  }
    delay(1);
 }
  return 0;
}

void gameOver(){
  
   Serial.println("GAME OVERR");
   Serial.println("level");
   Serial.println(gameIndex);
   gameIndex = 0;
   delay(200);  
   for(byte i =0; i<4; i++){
    tone(SPEAKER_PIN, gameTones[i]);
    delay(300);
   }
    noTone(SPEAKER_PIN);
    delay(500);
}

bool checkUserSequence(){
  for(byte i=0; i<gameIndex; i++){
    byte userPress = gameSequence[i];
    byte thisButton = readButtons();
    lightLedAndPlayTone(userPress);
    if(userPress!=thisButton)
       return false;
  }
  return true;
}

void playLevelUpSound(){
    tone(SPEAKER_PIN, NOTE_B5);
    delay(150);
    tone(SPEAKER_PIN, NOTE_C5);
    delay(150);
    tone(SPEAKER_PIN, NOTE_D5);
    delay(150);
    tone(SPEAKER_PIN, NOTE_E5);
    delay(150);
    tone(SPEAKER_PIN, NOTE_G5);
    delay(150);
    tone(SPEAKER_PIN, NOTE_E4);
    delay(150);
    noTone(SPEAKER_PIN); 
 }


