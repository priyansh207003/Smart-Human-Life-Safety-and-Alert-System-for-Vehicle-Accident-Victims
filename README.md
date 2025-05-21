
#include <LiquidCrystal_I2C.h>
LiquidCrystal_I2C lcd(0x27, 16, 2); 
#include <SoftwareSerial.h>
SoftwareSerial sim800(2,3); 
//for impact sensor
int impact_sensor = 8;
int impact_val = 1;
//for buzzer
int buzzer = 10;
//for push button
int push_btn = 7;
int push_val=1;
void setup() {
   Serial.begin(9600);	
  //for led
  lcd.init();
  lcd.backlight();
  sim800.begin(115200);
  delay(1000);  
  //for impact sensor
  pinMode(impact_sensor,INPUT);
  //for buzzer
  pinMode(buzzer,OUTPUT);
  //for push_button
  pinMode(push_btn,INPUT_PULLUP);
}
void loop() {
 lcd.setCursor(0, 0);
  lcd.print("You are safe");
impact_val = digitalRead(impact_sensor);
if(impact_val == 0){
  digitalWrite(buzzer,HIGH);
  Serial.println("impact");
  lcd.clear();
  lcd.print("Impact occur !!");
  delay(5000);
  lcd.clear();
  lcd.print("Are you ok ??");
  delay(5000);
  lcd.clear();
  lcd.print("hold the button ");
  digitalWrite(buzzer,LOW);
for(int i=0;i<2000;i++){
push_val = digitalRead(push_btn);
Serial.println(push_val);
}
if(push_val == 0){
    lcd.clear();
  lcd.print("You are safe!!");
  delay(5000);
  lcd.clear();
}else{
  lcd.clear();
  lcd.print("No response");
  delay(5000);
  lcd.clear();
  lcd.print("call and send");
  lcd.setCursor(0, 1);
  lcd.print("msg for help");
  delay(5000);
  //CALL AND SEND MSG WITH LOCATION 
   callandmsg();
}
}
}
void makeCall(String phoneNumber) {
  Serial.println("Making a call...");
  lcd.clear();
  lcd.print("Making a call...");
  // Send the AT command to initiate the call
  sim800.println("ATD" + phoneNumber + ";");  // ATD is the dial command, followed by the phone number
  delay(1000);  // Wait for the response
  Serial.println("Call in progress...");
  lcd.clear();
  lcd.print("Call in progress...");
  delay(30000);  // Wait for 30 seconds (You can change this duration)
  // Send the ATH command to hang up the call after 30 seconds
  sim800.println("ATH");  // Hang up the call
  delay(1000);  // Wait for the response
  Serial.println("Call ended.");
  lcd.clear();
  lcd.print("Call ended");
  delay(5000);
}
void sendSMS(String message, String phoneNumber) {
  Serial.println("Sending SMS...");
  lcd.clear();
  lcd.print("Sending SMS...");
  // Set the SIM800C module to text mode (SMS format)
  sim800.println("AT+CMGF=1");
  delay(1000);  // Wait for response
  // Send the phone number to the SIM800C module
  sim800.println("AT+CMGS=\"" + phoneNumber + "\"");
  delay(1000);  // Wait for response
  // Send the actual message
  sim800.println(message);
  delay(1000);  // Wait for response
  // Send Ctrl+Z (ASCII 26) to indicate end of message and send the SMS
  sim800.write(26);  // Ctrl+Z is ASCII code for end of message
  delay(1000);  // Wait for message to be sent
  Serial.println("Message sent!");
  lcd.clear();
  lcd.print("Message sent!");
  delay(5000);
}
void callandmsg(){
makeCall("+919039752050");  // Replace with the phone number you want to call
sendSMS("location","+919039752050");
lcd.clear();
}
