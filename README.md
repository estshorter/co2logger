CO2 and temperature logger using [`co2mon.hpp`](https://github.com/estshorter/co2mon).

indicator:
![indicators](https://raw.githubusercontent.com/estshorter/co2logger/images/display.png)

graph on ambient (disabled by default):
![ambient](https://raw.githubusercontent.com/estshorter/co2logger/images/ambient.png)

## How to build logger
create `configs.json` in `./logger`.
``` json
{
    "channel_id": 12345,
    "write_key": "YOUR_KEY",
    "monitoring_cycle_seconds": 4,
    "reporting_cycle_seconds": 30,
    "port": 30864
} 
```
`channel_id` and `write_key` can be obtained from [Ambient](https://ambidata.io/).

Debug build command:
``` sh
cmake -B build -S .
cmake --build build
```

### release build (for gcc and clang)
``` sh
cmake -DCMAKE_BUILD_TYPE=Release -B build -S . -GNinja
cd build && sudo ninja install
```
### release build (for MSVC)
``` sh
cmake --build build --config Release
```
## Run logger
Run `co2logger.exe`.
This program sends sensor data every "reporting_cycle_seconds" seconds to [Ambient](https://ambidata.io/).
