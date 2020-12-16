This is a logger using [`co2mon.hpp`](https://github.com/estshorter/co2mon).

## How to build logger
create `configs.json` in `./logger`.
``` json
{
    "channel_id": 12345,
    "write_key": "YOUR_KEY",
    "monitoring_cycle_seconds": 4,
    "reporting_cycle_seconds": 30
} 
```
`channel_id` and `write_key` can be obtained from [Ambient](https://ambidata.io/).

``` sh
cmake -B build -S .
cmake --build build
```

### release build (for gcc or clang)
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
This program sends sensor data every 30 seconds to [Ambient](https://ambidata.io/).
