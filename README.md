#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27,16,2);

int currentPage = 1;
bool isSelected = false;

void setup() {
  lcd.init();
  lcd.backlight();
  Serial.begin(9600);

  // Açılış mesajları
  lcd.setCursor(0,0);
  lcd.print("Merhaba Ben");
  lcd.setCursor(0,1);
  lcd.print("Bay Bilgili :)");
  delay(3000);

  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Soru cevaplamaya");
  lcd.setCursor(0,1);
  lcd.print("hazirim!");
  delay(3000);

  showPage(currentPage);
}

void loop() {
  if (Serial.available() > 0) {
    String input = Serial.readStringUntil('\n');
    input.trim();

    // Sayfa geçişleri
    if (input.equalsIgnoreCase("sayfa 1")) { currentPage = 1; isSelected = false; showPage(currentPage); }
    else if (input.equalsIgnoreCase("sayfa 2")) { currentPage = 2; isSelected = false; showPage(currentPage); }
    else if (input.equalsIgnoreCase("sayfa 3")) { currentPage = 3; isSelected = false; showPage(currentPage); }
    else if (input.equalsIgnoreCase("sayfa 4")) { currentPage = 4; isSelected = false; showPage(currentPage); }
    
    // Sayfa seçme
    else if (input.equalsIgnoreCase("seç")) { isSelected = true; executePage(currentPage); }

    // Hesap makinesi aktif ve sayı/girdi geldi
    else if (currentPage == 2 && isSelected) { calculate(input); }

    else {
      lcd.clear();
      lcd.setCursor(0,0);
      lcd.print("Gecersiz komut");
      delay(1500);
      showPage(currentPage);
    }
  }
}

// Sayfayı göster
void showPage(int page) {
  lcd.clear();
  switch(page){
    case 1:
      lcd.setCursor(0,0); lcd.print("Isteğiniz nedir?");
      lcd.setCursor(0,1); lcd.print("Sayfa 1"); break;
    case 2:
      lcd.setCursor(0,0); lcd.print("HESAP MAKINESI");
      lcd.setCursor(0,1); lcd.print("Sayfa 2"); break;
    case 3:
      lcd.setCursor(0,0); lcd.print("ANALIZ");
      lcd.setCursor(0,1); lcd.print("Sayfa 3"); break;
    case 4:
      lcd.setCursor(0,0); lcd.print("ISTEK");
      lcd.setCursor(0,1); lcd.print("Sayfa 4"); break;
  }
}

// Sayfa seçildiğinde işlemler
void executePage(int page){
  lcd.clear();
  switch(page){
    case 1:
      lcd.setCursor(0,0); lcd.print("Isteğiniz nedir?");
      lcd.setCursor(0,1); lcd.print("Sayfa 1 secildi"); break;
    case 2:
      lcd.setCursor(0,0); lcd.print("HESAP MAKINESI");
      lcd.setCursor(0,1); lcd.print("Secildi!"); break;
    case 3:
      lcd.setCursor(0,0); lcd.print("ANALIZ yapiliyor");
      lcd.setCursor(0,1);
      for(int i=0;i<4;i++){ lcd.print("."); delay(500); }
      break;
    case 4:
      lcd.setCursor(0,0); lcd.print("ISTEK");
      lcd.setCursor(0,1); lcd.print("Secildi!"); break;
  }
}

// Hesap makinesi fonksiyonu
void calculate(String input){
  double num1=0, num2=0, result=0;
  char op=0;

  // Basit parsing: 5+3 veya 12-4
  int opIndex = -1;
  if(input.indexOf('+') != -1) { op='+'; opIndex = input.indexOf('+'); }
  else if(input.indexOf('-') != -1) { op='-'; opIndex = input.indexOf('-'); }
  else if(input.indexOf('*') != -1) { op='*'; opIndex = input.indexOf('*'); }
  else if(input.indexOf('/') != -1) { op='/'; opIndex = input.indexOf('/'); }

  if(opIndex != -1){
    num1 = input.substring(0, opIndex).toDouble();
    num2 = input.substring(opIndex+1).toDouble();

    switch(op){
      case '+': result = num1 + num2; break;
      case '-': result = num1 - num2; break;
      case '*': result = num1 * num2; break;
      case '/': 
        if(num2 != 0) result = num1 / num2;
        else { lcd.clear(); lcd.print("Hata: 0'a bolme"); return; }
        break;
    }

    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print(input); // Örneğin 5+3
    lcd.setCursor(0,1);
    lcd.print("Sonuc: "); lcd.print(result);
  }
  else{
    lcd.clear();
    lcd.setCursor(0,0);
    lcd.print("Gecersiz islem");
  }
}

