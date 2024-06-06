#include <Servo.h>

#include <LiquidCrystal_I2C.h>

#include <IRremote.hpp>

#include "DHT.h"
#define DHTTYPE DHT11
#define DHTPIN A1
DHT dht(DHTPIN, DHTTYPE);

IRrecv irrecv(3);

LiquidCrystal_I2C lcd(0x27,16,2);

Servo win_left;
Servo win_right;
Servo main_door;
Servo toilet_door;

void setup()
{
  lcd.init();
  lcd.backlight();
  
  for (int i = 10; i <=13; i++){
    pinMode(i, OUTPUT);
  }
  
  pinMode(9, INPUT);
  
  pinMode(A0, INPUT);
  pinMode(A1, INPUT);
  pinMode(A2, INPUT);

  win_left.attach(8);
  win_right.attach(7);
  toilet_door.attach(6);
  main_door.attach(5);
  
  dht.begin();
  
  irrecv.enableIRIn();
  
  Serial.begin(9600);
}

int light_pwm;
int gas;
int temp = 17;
float celsium;
int intence;
float distanceCm;

byte _gas_;
byte _temp_;
byte _secur_;

byte temp_count;
byte gas_count;
byte secur_count;


byte lcd_status;

byte security_status;

byte light_status;

byte gas_alarm;

byte high_temp;

byte wins_opened;
byte main_door_opened;
byte toilet_door_opened;

void loop()
{ 
  phres(analogRead(A0));
  
  celsium = dht.readTemperature();

  //Serial.println(celsium);
  
  temp_sys(celsium, temp);
  
  gas = analogRead(A2);
  //Serial.println(gas);
  
  gas_sys(gas);
  
  //Serial.println("1");
  
  irremote();

  security_sys();
  
  //Serial.println("1");
}


void phres(int phres1){
  light_pwm = (phres1 / (974 / 255));
}


void temp_sys(int celsium, int temp){
  
  if (celsium > temp + 7 && celsium < 29.9){

    if (lcd_status == 0){
      lcd_status = 1;
    }
    
    temp_count = 0;
    _temp_ = 1;
    high_temp = 1;
  } 
  
  
  else if (celsium > temp + 13){
    temp_count = 0;
    _temp_ = 2;
    high_temp = 1;
    lcd_status = 1;
    wins_opened = 1;
  }
  
  else if (celsium <= 24){
    temp_count += 1;
    if (temp_count == 1){
      lcd_status = 0;
      lcd.clear();
      high_temp = 0;
    }
    _temp_ = 0;
  }
  
  //Serial.println("1");
  
  switch(_temp_){
    
    case 1:
      //Serial.println("1");
      
      if (lcd_status == 1){
        lcd.setCursor(0, 0);
        lcd.print("HIGH TEMP!       ");
        lcd.print(celsium);
        lcd.setCursor(0, 1);
        lcd.print("TEMP:");
        lcd.print(celsium);
      }
      
      digitalWrite(12, 0);

      break;
    
    case 2:

      win_left.write(0);
      win_right.write(0);
      
      digitalWrite(12, 1);

      lcd.setCursor(0, 0);
      lcd.print("TOO HIGH TEMP!    ");
      lcd.setCursor(0, 1);
      lcd.print("TEMP:");
      lcd.print(celsium);
      break;
    
    default:
      break;
    
  }
}


void gas_sys(int gas){
  //Serial.println("1");
  if (gas >= 400 && gas < 500){
    wins_opened = 1;
    lcd_status = 1;
    
    gas_count = 0;
    _gas_ = 1;
    gas_alarm = 0;
    
} else if (gas >= 500 && gas < 650){ 
    wins_opened = 1;
    lcd_status = 1;
  
    gas_count = 0;
    _gas_ = 2;
    gas_alarm = 1;
      
} else if (gas > 650){
    wins_opened = 1;
    lcd_status = 1;
  
    gas_count = 0;
    _gas_ = 3;
    gas_alarm = 1;
}

  else if (gas < 400){
    gas_count += 1;
    if (gas_count == 1){
      lcd_status = 0;
      lcd.clear();
    }
    gas_alarm = 0;  
    _gas_ = 0;
  }

  switch (_gas_){

    case 1:
      //Serial.println("1");

      win_left.write(0);
      win_right.write(0);
      
      lcd.setCursor(0,0);
      lcd.print("VENTILATE HOUSE! ");
      lcd.setCursor(0,1);
      lcd.print("GAS:");
      lcd.setCursor(5,1);
      lcd.print(gas);

      digitalWrite(12, 0);
      break;
    
    case 2:

      win_left.write(0);
      win_right.write(0);
       
      lcd.setCursor(0,0);
      lcd.print("GAS ALARM!       ");
      lcd.setCursor(0,1);
      lcd.print("GAS:");
      lcd.setCursor(5,1);
      lcd.print(gas);

      digitalWrite(12, 1);
      
      break;
    
    case 3:

      win_left.write(0);
      win_right.write(0);
      
      lcd.setCursor(0,0);
      lcd.print("FIRE!             ");
      lcd.setCursor(0,1);
      lcd.print("GAS:");
      lcd.setCursor(5,1);
      lcd.print(gas);

      digitalWrite(12, 1);
      
      break;
    
    default: 
      break;
  }
  //Serial.println("1");
}



void irremote(){
  
  if (IrReceiver.decode()){
    auto value= IrReceiver.decodedIRData.decodedRawData;
    Serial.println("SIGNAL!");
    switch (value){
      
      case 3910598400:      //0
        
        if (light_status == 0){
          //Serial.println(1);
          analogWrite(11, light_pwm);
          light_status = 1;
          break;
        }
      
        else if (light_status == 1){
          //Serial.println(0);
          analogWrite(11, 0);
          light_status = 0;
          break;
        }
        else{
          
          break;
          
        }
      
      case 4127850240:       //eq
        if (security_status == 0){
          security_status = 1;
        }
        else if (security_status == 1){
          security_status = 0;
        }
        break;
        
      case 3125149440:   //Main door

        if (main_door_opened == 0){
          Serial.println("open");
          main_door_opened = 1;
          main_door.write(0);
          break;
        }

        else if (main_door_opened == 1){
          Serial.println("close");
          main_door_opened = 0;
          main_door.write(90);
          break;
        }
        
        break;

      case 3108437760:

        if (wins_opened == 0){
          wins_opened = 1;
          win_left.write(0);
          win_right.write(0);
          break;
        }

        else if (wins_opened == 1){
          wins_opened = 0;
          win_left.write(90);
          win_right.write(90);
          break;
        }

        break;

      case 3091726080:

        if (toilet_door_opened == 0){
          toilet_door_opened = 1;
          toilet_door.write(0);
          break;
        }

        else if (toilet_door_opened == 1){
          toilet_door_opened = 0;
          toilet_door.write(90);
          break;
        }
      
      default: 
        Serial.println(value);
        break;
      
      }
    IrReceiver.resume();
  }
}


void security_sys(){
  while(security_status){

    wins_opened = 0;
    main_door_opened = 0;
    
    main_door.write(90);
    win_left.write(90);
    win_right.write(90);

    
    digitalWrite(10, 0);
    delayMicroseconds(2);
    digitalWrite(10, HIGH);
    delayMicroseconds(10);
    digitalWrite(10, 0);
  
    intence = pulseIn(9, HIGH);
    //distanceCm = intence * 0.0133 / 2;
    distanceCm = (intence / 2) / 29;

    if (distanceCm > 10 && distanceCm >= 25){
      if (lcd_status == 0){
        lcd_status = 1;
      }
      _secur_ = 1;
      secur_count = 0;
      security_status = 1;
      wins_opened = 0;
    }

    else if (distanceCm <= 10){
      
      _secur_ = 2;
      secur_count = 0;
      security_status = 1;
      wins_opened = 0;
    }

    else if (distanceCm > 25){
      _secur_ = 0;

      secur_count += 1;

      if (secur_count == 1){
        lcd_status = 0;
        lcd.clear();
        security_status = 0;
        wins_opened = 1;
      }
    }

    switch(_secur_){

      case 1:

        if (lcd_status == 0){

          lcd.setCursor(0, 0);
          lcd.print("SOMEONE AT DOOR");
          lcd.setCursor(0, 1);
          lcd.print("DISTANCE:");
          lcd.setCursor(10, 1);
          lcd.print(distanceCm);
          
        }

        digitalWrite(12, 0);
        break;

      case 2:

        lcd.setCursor(0, 0);
        lcd.print("SECURITY ALARM!  ");
        lcd.setCursor(0, 1);
        lcd.print("DISTANCE:");
        lcd.setCursor(10, 1);
        lcd.print(distanceCm);

        digitalWrite(12, 1);
        break;
    }

    break;
    
  }

}
