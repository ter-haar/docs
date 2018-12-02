## Czy python moze byc uzywany w IoT? Tak. 

Np AWS dostarcza pythonowe sdk, do obsługi AWS IoT (https://aws.amazon.com/iot/): 

* https://github.com/aws/aws-iot-device-sdk-python

Co najmniej kilka firm sprzedaje zestawy edukacyjne, przeznaczone do programowania w pythonie:

* https://pycom.io/
* https://store.micropython.org/

Skoro już wiemy, że python może być użyty w IoT, zobaczmy JAK może być użyty - na przykładzie 'inteligentnego' domu.

Sercem mojego inteligentnego domu jest home-assistant (albo w skrócie hass) (https://www.home-assistant.io/). Tak jak napisano hass to "Open source home automation that puts local control and privacy first. Powered by a worldwide community of tinkerers and DIY enthusiasts. Perfect to run on a Raspberry Pi or a local server.". 
Hass to aplikacja napisana w pythonie, uruchomiona na Raspberry Pi, która u mnie zarządza ogrzewaniem, gasi swiatła gdy nikogo nie ma w domu, zapala swiatło po wejsciu do pokoju. I tak dalej. 

Różne odmiany Raspberry Pi są chętnie stosowane w IoT i home automation. Raspbery Pi jest małe, pobiera niedużo energii, ma na pokładzie linuksa (a linuks ma pythona), więc zrobienie czegokolwiek jest proste, łatwe i przyjemne. 

Dzięki temu wykrywanie kto (i czy) jest w domu zrobiłem w jakieś dwa wieczory:

https://github.com/ter-haar/ble_gateway. 

Każdy z domowników ma przypisany do siebie itag (przypięty do kluczy), poza tym bramka wykrywa też telefony z włączonym bluetooth. "Gdy nie ma nikogo w domu, zgaś swiatła, skręć ogrzewanie, opuść rolety..."

Raspbery Pi + linux + python - jest to fajne, można na tym dużo zrobić, ale to nuuuda. To samo moge mieć na laptopie, czy na PC. Ciagle ten sam linux i ten sam python. Prawdziwa zabawa zaczyna się przy programowaniu naprawde małych rzeczy, gdzie nie ma gigabajtów ramu i megahercowych procesorów. I tutaj też możemy mieć pythona, ale już z pewnymi ograniczeniami. 

Sterowniki led:
* https://github.com/arendst/Sonoff-Tasmota/wiki/H801
* https://github.com/arendst/Sonoff-Tasmota/wiki/MagicHome-LED-strip-controller

Właczniki wifi:
* https://sonoff.itead.cc/en/products/sonoff/sonoff-basic
* https://sonoff.itead.cc/en/products/sonoff/sonoff-dual
* https://sonoff.itead.cc/en/products/residential/sonoff-t1-eu

Źarówki led
* https://www.itead.cc/sonoff-b1.html

i wiele, wiele innych używa bardzo popularnego w świecie IoT modułu ESP8266 (https://en.wikipedia.org/wiki/ESP8266). Ten malutki chip (80 MHz, 32 KiB instruction, 80 KiB user data RAM, zazwyczaj 4 MiB FLASH) wyposażony jest w wifi, więc jest idealny do sterowania. 

Fabryczne oprogramowanie jakie jest w w/w sterownikach zazwyczaj jest do niczego. Pozwala tylko na obsługę z jednej aplikacji i nie pozwala na integracje różnych urządzeń miedzy sobą. I tutaj zaczyna się prawdziwa zabawa, czyli micropython (https://micropython.org/) - "MicroPython is a full Python compiler and runtime that runs on the bare-metal".
Prawdę mówiąc, python zaczyna się jeszcze wcześniej. Trzeba skasowac fabryczny firmware w sterowniku którego chcemy użyć i wgrać własne firmware (np micropythona). I to tego też użyjemy pythona, czyli esptool: https://github.com/espressif/esptool

-----------------------------------------------------------------------------------------

Trzy testowe projekty, by porównać jak python, lua i C++ sprawdza się jako sterownik led
https://github.com/ter-haar/lua_mqtt_light
H801
https://github.com/ter-haar/h801_mqtt_light
H801
https://github.com/ter-haar/arduino_mqtt_light
cokolwiek co ma esp8266

PYTHON
------
zalety: 
* to jest python - pisanie jest proste łatwie i przyjemne
* webrepl
* aktualizacja kodu/konfiguracji przez mqtt

wady:
* słabe wbudowane biblioteki - mqtt co jakiś czas się rozłącza, a fast_led ma tylko podstawowe funkcje
* interpreter na esp8266 - pozwala 'skompilować' (compiles the code to bytecode, the bytecode is stored in RAM.) tylko niewielki program, coś większego wymaga kompilacji na PC - co jest bardziej skomplikowane od instalacji arduino ide (kompilatora arduiono C++).
* to jest interpreter, na dodatek ciągle coś działa w tle (konsola, garbage collector) - ciężko zrobić ścisłe zależności czasowe - animacja nie zawsze jest tak płynna jak powinna byc

LUA
---
zalety:
* aktualizacja kodu/konfiguracji przez mqtt

wady:
* nie lubie lua
* podobnie jak w pythonie, problem z zaleźnościami czasowymi (czasami przez 2-3 sekundy po wyslaniu komunikatu przez mqtt nic sie nie dzieje. We włączniku światła jest to strasznie wkurzające)

ARDUINO C++
-----------
zalety:
* ogromna baza dobrych bibliotek (dużo ludzi używa, dużo ludzi publikuje swój kod, łatwo znaleźć rozwiązenie problemów)
* nic nie dzieje się w tle. Jak coś ma byc co 5milisekund, to jest co 5milisekund.

wady:
* edycja kodu, kompilacja, czekanie, podłączone kable (ale można użyć OTA)

Wniosek
Python na ESP8266 się sprawdza (H801 z moim pythonowym firmware używam w domu od dłuższego czasu), ale ma ograniczenia - sterownik do cyfrowych ledów napisałem jednak w arduino C++



