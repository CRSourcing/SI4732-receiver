This firmware is designed for SI473x + ESP32 + ILI9341 receivers. The hardware is based
on the "common" schematic for diy SI4732 receivers (example in pics folder).

------------------------------------------------------------------------------------------

Getting Started:
Upload the .bin file to the ESP32.
Configure display and touchscreen.
If encoder direction is wrong, adjust in Config menu.
If SI5351 is used to generate 32768Hz clock, adjust in Config menu.
Configure WiFi:
Menu → More → Config → WiFi Cred.
Receiver works offline, but WiFi enables extra features.

------------------------------------------------------------------------------------------

Hardware "special" requirements:
1. Audio Input on GPIO32:
Required for waterfall, decoders, audio DSP, WiFi remote, KiwiSDR:
Needs 1–2 Vpp signal, centered at 1.65 V.
Sources: LM386 amplifier or single transistor amplifier (biased at 1.65 V).
Add 5–10 nF capacitor from GPIO32 to GND to reduce aliasing.

2. Audio Feedback (GPIO26, DAC)
Used for DSP output, touch sounds, and SD card playback.
Connect GPIO26 through a 10 nF capacitor + 15 kΩ resistor in series to audio amp input.
Resistor sets volume and value may vary.

3. SD Card
Connect to SPI bus (shared with display).
Pin mapping:
SCK -> GPIO18
MISO (SDO) -> GPIO19
MOSI (SDI) -> GPIO23
CS -> GPIO25
Keep wiring short (20 MHz bus).
Not all SD cards are compatible, power cycle  and try another if not recognized.


Battery Voltage indicator 
Read via GPIO33 with 2:1 resistive divider.
Max battery voltage: 6.6 V.
------------------------------------------------------------------------------------------

ESP 32 file System (LittleFS)
Several config files are stored on ESP32’s LittleFS. They can be downloaded/edited/re-uploaded via WiFi Sync:

MemoInfo.csv -> memory entries
eibi.lst -> EiBi station list
image.img -> last downloaded image
kiwisdr.url -> KiwiSDR server URLs
memory.csv -> station list (PicoRX compatible)
splash.jpg -> boot image
*.raw -> saved SSTV images (raw format)
*.bmp -> saved SSTV images (BMP format)

------------------------------------------------------------------------------------------


Software hints:

SI5351 support: Generates 32.768 kHz on CLK0 (enable in Config).
GPO1 support: HIGH in AM, LOW in FM (enable in Config).

Encoder:
Push = fine tune mode.
Push again = exit fine tune.


WiFi Sync: Syncs browser with the ESP32 file system. Download, edit, re‑upload config files, station lists and SSTV images.

WiFi interface: Allows to listen remotely. For best sound:
Adjust receiver volume first (avoid clipping).
Fine‑tune volume then with WiFiinterface slider.
This feature requires full ESP32 processing power → other functions may lag.
Exit via Freq -> Boot button.

Decoders:
CW: Best at 500–800 Hz. Align with waterfall red bars.
RTTY: Align mark/space with waterfall bars. Use encoder for fine tuning.
SSTV: Supports Martin & Scottie. Autodetect is based on sync interval and may fail.

Save SSTV images to SD card (BMP) or LittleFS (.raw or .bmp).
Limited storage: ~12 raw or 4 BMP files. Oldest overwritten when full.
Weatherfax: Experimental. IOC567 format only. Sync unstable.

User Interface
Indicators: Tap indicators below S‑Meter to change values.
Frequency digits: Tap upper/lower halves to adjust. Requires precise touch calibration.

