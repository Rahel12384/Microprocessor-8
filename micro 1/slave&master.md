
# نام آزمایش :
آزمایش Master و Slave با آردوینو

## هدف :
هدف از این آزمایش ایجاد ارتباط بین دو آردوینو با استفاده از پروتکل I2C به طوری که یکی به عنوان مستر و دیگری به عنوان سلو عمل کند و اطلاعات از مستر به سلو ارسال شود.

## وسایل مورد نیاز :
* 1 دو بورد اردینو
* 2 دو برد بورد
* 3 دو کابل  USB  برای اتصال بورد اردینو به کامپیوتر
* 4 جامپر 


  ## شرح آزمایش :
  در این آزمایش از پروتکل I2C استفاده شده است که به دستگاه‌ها امکان می‌دهد به صورت سریال داده‌ها را به یکدیگر ارسال و دریافت کنند. در این پروتکل یک دستگاه به عنوان "مستر" و دستگاه دیگر به عنوان "سلو" شناخته می‌شود. مستر مسئول ارسال فرمان و داده است و سلو داده‌ها را دریافت می‌کند.

## اتصال سخت‌افزاری:

    دو برد آردوینو به یکدیگر متصل شده‌اند.

    پین‌های SDA (پین A4 در آردوینو Uno) و SCL (پین A5 در آردوینو Uno) به یکدیگر متصل می‌شوند.

    پین‌های GND و VCC از هر دو برد آردوینو به یکدیگر متصل می‌شوند.

## مراحل انجام آزمایش:

   * راه‌اندازی و برنامه‌نویسی:

دو کد جداگانه برای مستر و سلو نوشته شد. مستر به طور مداوم داده‌ای را به سلو ارسال می‌کند و سلو آن داده را دریافت کرده و نمایش می‌دهد.

   * کد Master: در کد مستر، با استفاده از کتابخانه Wire.h، ارتباط I2C آغاز شده و داده‌ای به صورت متنی به سلو ارسال می‌شود.

     #include <Wire.h>

int value = 0;

void setup() {

  Wire.begin(); 
  
  Serial.begin(9600);

  pinMode(5, OUTPUT);

}

void loop() {

  int y = analogRead(A1);         
  
  value = map(y, 0, 1023, 0, 255);

  
  Wire.beginTransmission(8); 
  
  Wire.write("Master value :  ");         
  
  Wire.write(value);              
  
  Wire.endTransmission();   
  
  Wire.requestFrom(8, 1);    
  
  while (Wire.available()) { 
  
    int c = Wire.read(); 


    analogWrite(5, c);
    
    Serial.print("Slave value : ");
    
    Serial.println(c);        
  
  }

  delay(100);

}



   * کد Slave: در کد سلو، با استفاده از کتابخانه Wire.h و تنظیم آدرس 8 برای سلو، داده‌های دریافتی از مستر نمایش داده می‌شود.
   
     #include <Wire.h>

int x;

int value = 0;

void setup() {

  Wire.begin(8);
  
  Wire.onRequest(requestEvent);
  
  Wire.onReceive(receiveEvent);
  
  Serial.begin(9600);
  
  pinMode(9, OUTPUT);

}

void loop() {

  delay(100);
  
  analogWrite(9, x);
  
  int t = analogRead(A0);
  
  value = map(t, 0, 1023, 0, 255);

}

void receiveEvent() {

  while (1 < Wire.available()) {
  
    char c = Wire.read();
    
    Serial.print(c);
  
  }
    
  x = Wire.read();
  
  Serial.println(x);

}

void requestEvent() {
 
  Wire.write(value);}



پس از آپلود کدها روی هر دو آردوینو و اتصال سخت‌افزاری، دستگاه مستر به طور دوره‌ای پیام‌هایی را به سلو ارسال می‌کند. در این آزمایش، داده‌ها به صورت "Hello Slave" ارسال می‌شود.

روی دستگاه سلو، پنجره سریال باز می‌شود و داده‌های دریافتی نمایش داده می‌شود. هر بار که مستر پیام ارسال می‌کند، سلو پیام را در پنجره سریال نمایش می‌د



 ## شماتیک مدار:
![توضیح تصویر](https://github.com/Rahel12384/Microprocessor-8/blob/main/micro%201/88.png)



## خروجی آزمایش:
![alt text](https://github.com/Rahel12384/Microprocessor-8/blob/main/micro%201/VID_%DB%B2%DB%B0%DB%B2%DB%B5%DB%B0%DB%B1%DB%B0%DB%B7_%DB%B1%DB%B6%DB%B2%DB%B5%DB%B2%DB%B5.gif)

  
## نتیجه گیری:
آزمایش با موفقیت انجام شد و داده‌ها از مستر به سلو به درستی ارسال و دریافت شدند.

این آزمایش نشان داد که می‌توان از پروتکل I2C برای ارتباط بین دو آردوینو استفاده کرد.

ارتباط مستر و سلو با ارسال داده‌ها از مستر و دریافت آن‌ها توسط سلو برقرار شد.
