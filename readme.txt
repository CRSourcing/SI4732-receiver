 
This firmware is designed for SI473x + ESP32 + ILI9341 receivers.
The firmware will run on diy receivers that follow the "common" wiring scheme that has been published several times.
It will also run on some versions of the ATS25 receiver. "Air" version of the ATS25 are not supported.
CW, RTTY, SSTV and WEFAX decoders are included. A web interface is also included.
A KiwiSDR client lets you select a KiwiSDR and use it as a "radio within the radio".

------------------------------------------------------------------------------------------

Getting Started:
Upload the .bin file to the ESP32.
Configure display and touchscreen.
If encoder direction is wrong, adjust in Config menu.

Configure WiFi:
Menu → More → Config → WiFi Cred.
Receiver works offline, but WiFi needed for features.

------------------------------------------------------------------------------------------

Hardware "special" requirements:
1. Audio Input on GPIO32:
Required for waterfall, decoders, audio DSP, WiFi remote:
Needs 1–2 Vpp signal, centered at 1.65 V.
Sources: LM386 amplifier or single transistor amplifier (collector at 1.65 V).
Add 5–10 nF capacitor from GPIO32 to GND to reduce aliasing.

2. Audio Feedback (GPIO26 to audio input)
Used for all features that feed digital audio into the audio path.
Connect GPIO26 through a 10 nF capacitor + resistor in series to audio amp input.
Resistor value sets volume and value depends on the audio amplifier. Resistor value needs to be figured out.
Good starting points are 100K for high impedance audio input and 10K for low impedance.

3. SD Card
Connect to SPI bus (shared with display).
Pin mapping:
SCK -> GPIO18
MISO (SDO) -> GPIO19
MOSI (SDI) -> GPIO23
CS -> GPIO25
Keep wiring short (20 MHz bus).
Not all SD cards are compatible, power cycle  and try another if not recognized.

4. Audio DSP
Audio DSP reads audio from the ADC and plays it back via the DAC. To avoid mixing "normal" audio and DAC audio, the normal audio must not reach the audio amp input when the DSP is in use. This can be done with the mute transistor and a resistor between the mute transistor and the audio amplifier input, or an audio switch that opens the connection btw the ESP32 audio out and the audio amplifier input.

5. Battery Voltage indicator
Reads via GPIO33 with 2:1 resistive divider.
Max battery voltage: 6.6 V.
------------------------------------------------------------------------------------------

ESP 32 file System (LittleFS)
Several config files are stored on ESP32’s LittleFS. They can be downloaded/modified/re-uploaded via WiFi Sync:

MemoInfo.csv -> memory entries
eibi.lst -> EiBi station list
image.img -> last downloaded image
kiwisdr.url -> KiwiSDR server URLs
iradio.url -> Internet radio server URLs
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
Fine‑tune volume then with WiFi interface slider.
This feature requires full ESP32 processing power -> other functions may lag or be unavailable.
Exit via Freq -> Boot button.

Decoders:
CW: Best at 500–800 Hz. Align with waterfall red bars.
RTTY: Align mark/space with waterfall bars. Mark should be set to 2125Hz. While decoding use encoder for fine tuning.
SSTV: Supports Martin & Scottie. Autodetect is based on sync interval and may fail.
Weatherfax: Experimental. IOC567 format only. Sync unstable.

SSTV files:
Saves SSTV images to SD card (BMP) or LittleFS (.raw or .bmp).
Limited storage: ~12 raw or 4 BMP files. Oldest file gets overwritten when full.


KiwiSDR: Connects to 1 of 10 selectable KiwiSDR servers using the current frequency/mode. KiwiSDR servers can be changed by modifying the config file kiwisdr.url.
Not all will work since this client uses a rather primitive implementation of the (undisclosed) communication protocol between server and client. Also, servers may change
url or no longer be available.
To change a server, check http://rx.linkfanel.net/ and http://kiwisdr.com/public/. Try the new server first in a browser and
use a server that does not use cloudflare or similar bot protector.
For SSB and CW the SI4732 frequency needs to be precisely adjusted (Config -> Crystal Offset), otherwise it will cause a frequency offset with the Kiwi server.
The client can now be tuned with the encoder. The first tuning changes will be slow (several seconds) until the internal audio buffer has been adjusted.
Later changes will take around 1 second. Not all servers allow tuning with the encoder.

DLY- and DLY+ may need to be adjusted so that the green number above the NBFM button shows a value of 171 or slightly below. Values > 171 will cause dropouts.
Values lower than 171 will change pitch.
The client may not function on every hardware, the streaming and decoding chain pushes the ES32 to it's limits.


Internet radio: Experimental. Servers can be changed by modifying the config file iradio.url. Currently only mp3 encoding is supported. Faster streams (>192Kbit)
will most likely stutter.


FlightRadar:

FlightRadar used an external downconverter to receive airband frequencies and displays the aircraft position on the screen. Aircrafta can be displayed without the downconverter.
The converter's crystal frequency needs to be set in the config menu. Valid entries are between 99 and 120MHz. 10 hotkeys for air frequencies are available.
To configure their frequencies, download the file atcfreqs.lst from the receiver, edit the file (description in the file) and re-upload it to the receiver.
The squelch should set to slightly closed before using the FlightRadar module.

Location: Configure the location you want to see on the radar screen's center in the Config Menu. Tap on Lat/Lon and enter coordinates. For demo purposes, the Lat/Lon of London has been entered. The radar's range can be zoomed between 3 and 50km.The radar will show the aircraft's  call sign and it's flight trail. Trails will accumulate, to remove them, tap on Clear Trails. To see aircraft details, tap on the aircraft triangle (good touchscreen calibration is needed). Tapping on "Map" will put the radar in map mode and it will display the aircraft on an underlaying map, if a map was uploaded to LittleFS.

These maps must be manually created and loaded onto the LittleFS. 5 maps, one for each zoom level, are required. They must be 200*200 pixels wide, jpg format and have the location in it's center. They must cover exactly the radar's range, for example the map for 50km radius (50000.jpg) must show a 100x100km square. Contact me if you want to know how to create these maps.
Scan: To scan through the 10 aircraft frequencies, drag the white squelch bar until the squelch closes and tap on the "Scan" button. 



User Interface
Indicators: Tap indicators below S‑Meter to change values.
Frequency digits: Tap upper/lower halves to change frequency. Requires precise touch calibration.
Tap left or right of "Step:..." to step frequency up/down.



