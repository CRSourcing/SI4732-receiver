This firmware integrates ESP32 + SI473x + ILI9341 display with audio DSP, CW/RTTY/SSTV decoders, SD card storage, and WiFi sync. The hardware is based
on the "common" schematic for diy SI4732 receivers (example in pics folder).


Audio paths needed:
SI4732 audio out via amplifier to GPIO32 (ADC, centered at 1.65 V).
GPIO26 via 10 nF + 15 kΩ to audio amplifier input.
Encoder pushbutton GPIO requires a resistive divider (see schematic).




Getting Started:
Upload the .bin firmware to ESP32.
Configure display and touchscreen.
If encoder direction is reversed, adjust in Config menu.

Configure WiFi:
Menu → More → Config → WiFi Cred.
Receiver works offline, but WiFi enables extra features.


Hardware details:
Audio Input (GPIO32, ADC)
Required for waterfall, decoders, and audio DSP.
Needs 1–2 Vpp signal, centered at 1.65 V.
Sources: LM386 amplifier or single transistor amplifier (biased at 1.65 V).
Add 5–10 nF capacitor from GPIO32 to GND to reduce aliasing.
Audio Feedback (GPIO26, DAC)
Used for DSP output, touch sounds, and SD card playback.

Connect via 10 nF capacitor + 15 kΩ resistor in series to audio amp input.
Resistor sets volume and value may vary.

SD Card
Connect to SPI bus (shared with display).
Pin mapping:
SCK → GPIO18
MISO (SDO) → GPIO19
MOSI (SDI) → GPIO23
CS → GPIO25
Keep wiring short (20 MHz bus).
Not all SD cards are compatible; power cycle if not recognized.


Encoder
Push = fine tune mode.
Push again = exit fine tune.

Battery Voltage
Read via GPIO33 with 2:1 resistive divider.
Max battery voltage: 6.6 V.

File System (LittleFS)
Files stored on ESP32’s LittleFS (download/edit via WiFi Sync):

MemoInfo.csv → memory entries
eibi.lst → EiBi station list
image.img → last downloaded image
kiwisdr.url → KiwiSDR server URLs
memory.csv → station list (PicoRX compatible)
splash.jpg → boot image
sstv123.raw → saved SSTV images (raw format)
sstv123.bmp → saved SSTV images (BMP format)

Software hints:

SI5351 support: Generates 32.768 kHz on CLK0 (enable in Config).
GPO1 support: HIGH in AM, LOW in FM (enable in Config).

WiFi Sync: Syncs browser with the ESP32 file system. Download/edit/re‑upload station lists and SSTV images.

WiFi interface: Allows to listen remotely. For best sound:
Adjust receiver volume first (avoid clipping).
Fine‑tune volume then with WiFiinterface slider.
Uses full ESP32 processing power → other functions may lag.
Exit via Freq → Boot button.

Decoders:
CW: Best at 500–800 Hz. Align with waterfall red bars.
RTTY: Align mark/space with waterfall bars. Use encoder for fine tuning.
SSTV: Supports Martin & Scottie. Autodetect based on sync interval.

Save SSTV images to SD card (BMP) or LittleFS (.raw or .bmp).

Limited storage: ~12 raw or 4 BMP files. Oldest overwritten when full.
Weatherfax: Experimental. IOC567 format only. Sync unstable.

User Interface
Indicators: Tap below S‑Meter to change values.
Frequency digits: Tap upper/lower halves to adjust. Requires precise touch calibration.

