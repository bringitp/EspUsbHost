This is a library for using USB Host with ESP32.

## Target board
- ESP32-S3-DevKitC
- M5Stack ATOMS3

## TODO
- Shift Lock
- Keyrepeat 


## function
- USB Keybord
- USB Mouse

## Usage
```c
#include "EspUsbHost.h"

class MyEspUsbHost : public EspUsbHost {
  void onKeyboardKey(uint8_t ascii, uint8_t keycode, uint8_t modifier) {
    if (' ' <= ascii && ascii <= '~') {
      Serial.printf("%c", ascii);
    } else if (ascii == '\r') {
      Serial.println();
    }
  };
};

MyEspUsbHost usbHost;

void setup() {
  Serial.begin(115200);
  delay(500);

  usbHost.begin();
  usbHost.setHIDLocal(HID_LOCAL_Japan_Katakana);
}

void loop() {
  usbHost.task();
}
```

## ATOMS3
```c
#include <M5Unified.h>
#include "EspUsbHost.h"

class MyEspUsbHost : public EspUsbHost {
  void onKeyboardKey(uint8_t ascii, uint8_t keycode, uint8_t modifier) {
    M5.Display.startWrite();
    // 以前の内容を上書きするために、同じ幅のスペースで埋める
    M5.Display.setCursor(0, 0); // カーソルを先頭に移動
    M5.Display.printf("          "); // 以前の内容と同じ幅のスペース
    M5.Display.setCursor(0, 0); // カーソルを先頭に移動
 
    M5.Display.printf("%i %i", keycode, modifier);
    M5.Display.endWrite();
  }
};

MyEspUsbHost usbHost;

void setup(void) {
  auto cfg = M5.config();
  M5.begin(cfg);

  M5.Display.setTextSize(4);

  M5.Display.startWrite();
  M5.Display.print("ON!:");
  M5.Display.endWrite();

  usbHost.begin();
  usbHost.setHIDLocal(HID_LOCAL_Japan_Katakana);
}

void loop(void) {
  delay(1);
  M5.update();
  M5.Display.startWrite();
  delay(10);
  usbHost.task();
  M5.Display.endWrite();
  M5.Display.display();
}


```

## Virtual function

### common

- virtual void onData(const usb_transfer_t *transfer);
- virtual void onGone(const usb_host_client_event_msg_t *eventMsg);

### Keyboard

- virtual uint8_t getKeycodeToAscii(uint8_t keycode, uint8_t shift);  
- virtual void onKeyboard(hid_keyboard_report_t report, hid_keyboard_report_t last_report);  
~~- virtual void onKeyboardKey(uint8_t ascii, uint8_t keycode, uint8_t modifier);~~  
- virtual void onKeyboardKey(uint8_t ascii, uint8_t keycode, uint8_t Alt,Shift,Win,Ctrl);  

### Appendix
![20240303_144128113](https://github.com/bringitp/EspUsbHost/assets/141851166/1bab59ca-f9b2-4772-b1e2-0ec7599a4230)


### Mouse

- virtual void onMouse(hid_mouse_report_t report, uint8_t last_buttons);
- virtual void onMouseButtons(hid_mouse_report_t report, uint8_t last_buttons);
- virtual void onMouseMove(hid_mouse_report_t report);


