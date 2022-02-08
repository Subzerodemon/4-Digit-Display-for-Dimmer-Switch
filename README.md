# 4-Digit-Display-for-Dimmer-Switch
Need Help displaying 0-100% on 4 Digit Display when using a rotary angle sensor to adjust the dimmness of a red LED. Right now the display shows 4095, instead of 100 when the light is at its maximum. When the light is off it displays 0 like it should. I am new to coding and am not sure what to do. 



#include "TM1637.h"

#define CLK 38

#define DIO 37

#define ROTARY_ANGLE_P  24
#define Full_Angle 300
#define ADC_REF 5
#define GROVE_VCC 5

TM1637 tm1637(CLK, DIO);
int analog_value=0;

int8_t bits[4]={0};

void setup() {
  // put your setup code here, to run once:
Serial.begin(9600);
pinMode(RED_LED, OUTPUT);
pinsInit();
tm1637.init();
tm1637.set(BRIGHT_TYPICAL);
}

void loop() {
  // put your main code here, to run repeatedly: 
  int degrees;
  degrees= getDegree();
  Serial.println(degrees);
  

  int brightness;
  brightness= map(degrees, 0, Full_Angle, 0, 255);
  controlBrightness(brightness);

  delay(500);

  
  analog_value=analogRead(ROTARY_ANGLE_P);
  memset(bits,0,4);
  for (int i=3; i>=0; i--){
    bits[i]=analog_value % 10;
  analog_value=analog_value/10;
  tm1637.display(i,bits[i]);
  }
  delay(100);
  
}
void pinsInit() {
  pinMode(ROTARY_ANGLE_P, INPUT);
  pinMode(RED_LED, OUTPUT);
  
}

void controlBrightness(int brightness) {
  analogWrite(RED_LED, brightness);
}

int getDegree()
{
  int sensor_value=analogRead(ROTARY_ANGLE_P);
  float voltage;
  voltage = (float)sensor_value*ADC_REF/1023;
  float degrees= (voltage*Full_Angle)/GROVE_VCC;
  return degrees;
}
