# Skeleton-super-Girl
Dead Inside

Hópmeðlimir:
Björgvin Þeyr Guðmusdsson
Einar Andrés Þórarinsson
Bjarki Freyr Ragnarsson


Við þrír bjuggum til saman beinagrindi sem býr yfir þeim eiginleika að hreyfa sig upp og niður og vera með heljarinnar óhljóð þegar eithver kemur innan við 88 centimetra fyrir framan hana.
Hún hefur líka rauð augu sem blikka og munn sem hreyfist. Það er einfalt og þægilegt að breyta LED preonum í hvaða lit sem er.

Við notuðum Servo motor, DC motor, MP3 spilara, Rauð LED, Fjarlægðaskynjara, hátalara og Aurdino UNO.
Okkur fannst þetta afskablega skemmtilegt verkefni vegna þess að við höfðum svo mikið frelsi til þess að gera verkefnið eins og okkur langaði og við erum mjög ánægðir með hvernig þetta endaði hjá okkur.

Myndir
==========================

![20230515_143928](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/66cb2f9e-3f35-466b-93a4-63cfab79eb81)

![20230515_133224](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/d2a3c9b4-599b-4049-84f4-16db3e0ee34d)
![20230515_133217](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/a3b75dd4-bdf1-4d8d-8c7a-a1b6e55908a0)
![20230515_133211](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/65cfa810-6b4a-49b9-9ae6-2aff37292ca2)
![20230515_133208](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/7fe70d1b-bd69-452f-926b-400d6deb6735)
![20230515_133205](https://github.com/Dedneyder/Skeleton-super-Girl/assets/129172410/8a2cc802-01ac-4276-816b-2ad025da6177)



vídeo
===================================================================
https://www.youtube.com/watch?v=QffYzWWR1zU





Kóðinn
================================================================================================================
#include "tdelay.h"
#include "Arduino.h"
#include "SoftwareSerial.h"
#include "DFRobotDFPlayerMini.h"
#include <Servo.h>
 

int LED2 = 7;
int LED = 8;

int motor_pinni = 3;

SoftwareSerial mySoftwareSerial(12, 13);  // RX, TX  

const int STEFNA_A = 5;
const int STEFNA_B = 6;
const int HRADI = 11;  

const int ECHO = 2; 
const int TRIG =4; 

//--------------------------------------------------------------------------------------
DFRobotDFPlayerMini myDFPlayer;                        // útfært neðar
TDelay spilun(10000);    




    Servo motor; // bý til tilvik af Servo klasanum
 // pinninn sem ég nota til að stjórna mótornum
TDelay updown(300);







// svipað og listi í python, geymir stefnurnar sem mótorinn á að fara í og í hvaða röð
int motor_stefnur[] = {0, 200, 0, 180}; 
int motor_stefnu_fjoldi = 4; // breytan geymir hversu margar stefnur eru í listanum
int motor_stefnu_teljari = 0; // breytan heldur utan um í hvaða stefnu mótorinn á að benda
TDelay motor_bid(100); // bíða í hálfa sekúndu á milli hreyfinga



bool LED_on2 = false;



bool LED_on = false;

// Kveikt á LED í 750 ms og svo slökkt í 250 ms
int on_timi2 = 200;
int off_timi2 = 250;

TDelay led_delay2(on_timi2); // byrjum á að hafa kveikt


// Kveikt á LED í 750 ms og svo slökkt í 250 ms
int on_timi = 1200;
int off_timi = 1000;

TDelay led_delay(on_timi); // byrjum á að hafa kveikt



int distance = 0;  
int fjarlaegd();  // fall sem sér um fjarlægðamælinguna, skilar fjarlægð í cm. 


void setup() {
    motor.attach(motor_pinni); // segi servo tilvikinu hvaða pinna á að nota
    motor.write(motor_stefnur[motor_stefnu_teljari]); // í þessu tilfelli á mótorinn að byrja í 0
    Serial.begin(9600); 
      mySoftwareSerial.begin(9600);  
    pinMode(7,OUTPUT);
    pinMode(12,OUTPUT);
      pinMode(HRADI, OUTPUT);
      pinMode(STEFNA_A, OUTPUT);
      pinMode(STEFNA_B, OUTPUT);
    pinMode(TRIG,OUTPUT);
    pinMode(ECHO,INPUT);


   if (!myDFPlayer.begin(mySoftwareSerial)) {  
     //while(true);
  }
   myDFPlayer.volume(23);        // Set volume value. From 0 to 30
}

void playSound() {
  if (spilun.timiLidinn() == true) {
    myDFPlayer.play(1);     // Play mp3 
  }
}

void vinstri(int hradi) {

    digitalWrite(STEFNA_A, HIGH);
    digitalWrite(STEFNA_B, LOW);
    analogWrite(HRADI, hradi);
    
}



void servomot() {
  if(motor_bid.timiLidinn()) {
    // uppfæra stefnu_teljara breytuna, modulus notað til að talan verði
    // aldrei hærri en fjöldi stefnanna sem eru í listanum
    motor_stefnu_teljari = (motor_stefnu_teljari + 1) % motor_stefnu_fjoldi;
    // veljum svo rétta stefnu úr listanum
    motor.write(motor_stefnur[motor_stefnu_teljari]);
  }
}



void Blikk(){
       if(led_delay2.timiLidinn()) {
    
      if(LED_on2) { // ef það er kveikt núna
      LED_on2 = false;
      led_delay2.setBidtimi(off_timi2);
    
    } else { // það er slökkt á perunni
      LED_on2 = true;
      led_delay2.setBidtimi(on_timi2);
}
       }
      if(led_delay.timiLidinn()) {
    
      if(LED_on) { // ef það er kveikt núna
      LED_on = false;
      led_delay.setBidtimi(off_timi);
    
    } else { // það er slökkt á perunni
      LED_on = true;
      led_delay.setBidtimi(on_timi);
    }
      }
  digitalWrite(LED, LED_on);
  digitalWrite(LED2, LED_on2);
}





void loop()  {

     distance = fjarlaegd();           // fáum fjarlægðamælingu í cm.
    Serial.print("Fjarlaegd: ");     // til að sjá mælingu í skjánum
    Serial.println(fjarlaegd());     // til að sjá mælingu í skjánum  
 if (distance <=88){  
 playSound();
 vinstri(200);
 servomot();
 Blikk();
 }
 }



















int fjarlaegd() {
  
    // sendir út púlsa
    digitalWrite(TRIG,LOW);
    delayMicroseconds(50); // of stutt delay til að skipta máli
    digitalWrite(TRIG,HIGH);
    delayMicroseconds(50); // of stutt delay til að skipta máli
    digitalWrite(TRIG,LOW);

    // mælt hvað púlsarnir voru lengi að berast til baka
    return (int)pulseIn(ECHO,HIGH)/58.0; // deilt með 58 til að breyta í cm

}
