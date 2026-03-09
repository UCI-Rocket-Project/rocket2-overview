# Avionics data packets
All data packets for communicationa between boards or to the webservice are defined in here.

### GSE Command Packet
```c
struct gseCommand {
    bool igniter0Fire;
    bool igniter1Fire;
    bool alarm;
    bool solenoidStateGn2Fill;
    bool solenoidStateGn2Vent;
    bool solenoidStateGn2Disconnect;
    bool solenoidStateMvasFill;
    bool solenoidStateMvasVent;
    bool solenoidStateMvasOpen;
    bool solenoidStateMvasClose;
    bool solenoidStateLoxVent;
    bool solenoidStateLngVent;
    uint32_t crc;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| igniter0Fire | `bool` | Fire igniter 0 |
| igniter1Fire | `bool` | Fire igniter 1 |
| alarm | `bool` | Sound alarm |
| solenoidStateGn2Fill | `bool` | Nitrogen vehicle fill, normally closed |
| solenoidStateGn2Vent | `bool` |  Nitrogen GSE panel vent, normally closed |
| solenoidStateGn2Disconnect | `bool` | Actuate nitrogen quick disconnect |
| solenoidStateMvasFill | `bool` | MVAS line fill, normally closed |
| solenoidStateMvasVent | `bool` | MVAS line vent, normally closed |
| solenoidStateMvasOpen | `bool` | Open MVAS, normally closed |
| solenoidStateMvasClose | `bool` | Close MVAS, normally closed |
| solenoidStateLoxVent | `bool` | Liquid oxygen GSE panel vent, normally open |
| solenoidStateLngVent | `bool` | Liquid methane GSE panel vent, normally open |
> All fields shall be filled with valid command. For solenoids, a value of `1` or `true` **always** corresponds to an energized value. A value of `0` or `false` **always** corresponds to a non-energized valve, regardless of solenoid type.

### GSE Data Packet
```c
struct GseData {
    uint32_t timestamp;
    bool igniterArmed;
    bool igniter0Continuity;
    bool igniter1Continuity;
    bool igniterInternalState0;
    bool igniterInternalState1;
    bool alarmInternalState;
    bool solenoidInternalStateGn2Fill;
    bool solenoidInternalStateGn2Vent;
    bool solenoidInternalStateGn2Disconnect;
    bool solenoidInternalStateMvasFill;
    bool solenoidInternalStateMvasVent;
    bool solenoidInternalStateMvasOpen;
    bool solenoidInternalStateMvasClose;
    bool solenoidInternalStateLoxVent;
    bool solenoidInternalStateLngVent;
    float supplyVoltage0 = std::nanf("");
    float supplyVoltage1 = std::nanf("");
    float solenoidCurrentGn2Fill = std::nanf("");
    float solenoidCurrentGn2Vent = std::nanf("");
    float solenoidCurrentGn2Disconnect = std::nanf("");
    float solenoidCurrentMvasFill = std::nanf("");
    float solenoidCurrentMvasVent = std::nanf("");
    float solenoidCurrentMvasOpen = std::nanf("");
    float solenoidCurrentMvasClose = std::nanf("");
    float solenoidCurrentLoxVent = std::nanf("");
    float solenoidCurrentLngVent = std::nanf("");
    float temperatureLox = std::nanf("");
    float temperatureLng = std::nanf("");
    float pressureGn2 = std::nanf("");
    float pressureChamber = std::nanf("");
    uint32_t crc;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| igniterArmed | `bool` | | Igniter arming key state, `1` for armed |
| igniter0Continuity | `bool` | | Igniter 0 continuity, `1` for continuity detected |
| igniter1Continuity | `bool` | | Igniter 1 continuity, `1` for continuity detected |
| igniterInternalState0 | `bool` | | Igniter 0 fire state feedback |
| igniterInternalState1 | `bool` | | Igniter 1 fire state feedback |
| alarmInternalState1 | `bool` | | Alarm state feedback |
| solenoidInternalStateGn2Fill | `bool` | | Nitrogen vehicle fill solenoid state feedback |
| solenoidInternalStateGn2Vent | `bool` | | Nitrogen GSE panel vent solenoid state feedback |
| solenoidInternalStateGn2Disconnect | `bool` | | Nitrogen quick disconnect solenoid state feedback |
| solenoidInternalStateMvasFill | `bool` | | MVAS line fill solenoid state feedback |
| solenoidInternalStateMvasVent | `bool` | | MVAS line vent solenoid state feedback |
| solenoidInternalStateMvasOpen | `bool` | | MVAS open solenoid state feedback |
| solenoidInternalStateMvasClose | `bool` | | MVAS close solenoid state feedback |
| solenoidInternalStateLoxVent | `bool` | | Liquid oxygen GSE panel vent solenoid state feedback |
| solenoidInternalStateLngVent | `bool` | | Liquid methane GSE panel vent solenoid state feedback |
| supplyVoltage0 | `float` | $V$ | Power supply 0 voltage |
| supplyVoltage1 | `float` | $V$ | Power supply 1 voltage |
| solenoidCurrentGn2Fill | `float` | $A$ | Nitrogen vehicle fill solenoid current feedback |
| solenoidCurrentGn2Vent | `float` | $A$ | Nitrogen GSE panel vent solenoid current feedback |
| solenoidCurrentGn2Disconnect | `float` | $A$ | Nitrogen quick disconnect solenoid current feedback |
| solenoidCurrentMvasFill | `float` | $A$ | MVAS line fill solenoid current feedback |
| solenoidCurrentMvasVent | `float` | $A$ | MVAS line vent solenoid current feedback |
| solenoidCurrentMvasOpen | `float` | $A$ | MVAS open solenoid current feedback |
| solenoidCurrentMvasClose | `float` | $A$ | MVAS close solenoid current feedback |
| solenoidCurrentLoxVent | `float` | $A$ | Liquid oxygen GSE panel vent solenoid current feedback |
| solenoidCurrentLngVent | `float` | $A$ | Liquid methane GSE panel vent solenoid current feedback |
| temperatureLox | `float` | $\degree C$ | Liquid oxygen temperature |
| temperatureLng | `float` | $\degree C$ | Liquid methane temperature |
| pressureGn2 | `float` | $V$ | Nitrogen bottle pressure, max range 5000 psi |
| pressureChamber | `float` | $V$ | Combustion champer pressure, max range 1000 psi |
> `timestamp`, `igniterArmed`, `igniterContinuity`, and `solenoidInternalState*` fields are required. All other fields are optional and shall remain its default value to indicate no data.

> Pressure data is reported in Volts, calibrated for device frontend and ADC, conversion to pressure shall be handled by ground systems.

### ECU Command Packet
```c
struct ecuCommand {
    bool solenoidStateCopvVent;
    bool solenoidStatePv1;
    bool solenoidStatePv2;
    bool solenoidStateVent;
    uint32_t crc;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| solenoidStateCopvVent | `bool` | COPV vent, normally closed |
| solenoidStatePv1 | `bool` | DLPR Dome fill, normally closed |
| solenoidStatePv2 | `bool` | DLPR Dome vent, normally open |
| solenoidStateVent | `bool` | LOX & LNG vent, normally closed |
> All fields shall be filled with valid command. For solenoids, a value of `1` or `true` **always** corresponds to an energized value. A value of `0` or `false` **always** corresponds to a non-energized valve, regardless of solenoid type.

### ECU Data Packet
```c
struct ecuData {
    uint32_t timestamp;
    float packetRssi;
    float packetLoss;
    bool solenoidInternalStateCopvVent;
    bool solenoidInternalStatePv1;
    bool solenoidInternalStatePv2;
    bool solenoidInternalStateVent;
    float supplyVoltage = std::nanf("");
    float batteryVoltage = std::nanf("");
    float solenoidCurrentCopvVent = std::nanf("");
    float solenoidCurrentPv1 = std::nanf("");
    float solenoidCurrentPv2 = std::nanf("");
    float solenoidCurrentVent = std::nanf("");
    float temperatureCopv = std::nanf("");
    float pressureCopv = std::nanf("");
    float pressureLox = std::nanf("");
    float pressureLng = std::nanf("");
    float pressureInjectorLox = std::nanf("");
    float pressureInjectorLng = std::nanf("");
    float angularVelocityX = std::nanf("");
    float angularVelocityY = std::nanf("");
    float angularVelocityZ = std::nanf("");
    float accelerationX = std::nanf("");
    float accelerationY = std::nanf("");
    float accelerationZ = std::nanf("");
    float magneticFieldX = std::nanf("");
    float magneticFieldY = std::nanf("");
    float magneticFieldZ = std::nanf("");
    float temperature = std::nanf("");
    float altitude = std::nanf("");
    float ecefPositionX = std::nanf("");
    float ecefPositionY = std::nanf("");
    float ecefPositionZ = std::nanf("");
    float ecefPositionAccuracy = std::nanf("");
    float ecefVelocityX = std::nanf("");
    float ecefVelocityY = std::nanf("");
    float ecefVelocityZ = std::nanf("");
    float ecefVelocityAccuracy = std::nanf("");
    uint32_t crc;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| packetRssi | `float` | $dBm$ | Radio packet receive signal strength indicator |
| packetLoss | `float` | | Radio packet loss rate ratio |
| solenoidInternalStateCopvVent | `bool` | | COPV vent solenoid state feedback |
| solenoidInternalStatePv1 | `bool` | | DLPR Dome fill solenoid state feedback |
| solenoidInternalStatePv2 | `bool` | | DLPR Dome vent solenoid state feedback |
| solenoidInternalStateVent | `bool` | | LOX & LNG vent solenoid state feedback |
| supplyVoltage | `float` | $V$ | Power supply voltage |
| batteryVoltage | `float` | $V$ | Battery supply voltage |
| solenoidCurrentCopvVent | `float` | $A$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `float` | $A$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `float` | $A$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `float` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureCopv | `float` | $\degree C$ | COPV temperature |
| pressureCopv | `float` | $V$ | COPV pressure, max range 5000 psi |
| pressureLox | `float` | $V$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `float` | $V$ | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | `float` | $V$ | Injector liquid oxygen pressure, max range 1000 psi |
| pressureInjectorLng | `float` | $V$ | Injector methane pressure, max range 1000 psi |
| angularVelocityX | `float` | $rad/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `float` | $rad/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `float` | $rad/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `float` | $m/s^2$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `float` | $m/s^2$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `float` | $m/s^2$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `float` | $T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `float` | $T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `float` | $T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `float` | $\degree C$ | Environment temperature |
| altitude | `float` | $m$ | Mean sea level altitude |
| ecefPositionX | `float` | $m$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `float` | $m$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `float` | $m$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `float` | $m$ | GNSS estimated position accuracy |
| ecefVelocityX | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `float` | $m/s$ | GNSS estimated velocity accuracy |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.

> Pressure data is reported in Volts, calibrated for device frontend and ADC, conversion to pressure shall be handled by ground systems.

## Standardized Memory Format
All data stored into onboard memory will use the standardized packet. All packets are 64 bytes aligned and packed little-endian. Checksums are IBM CRC-16 (polynomial `0x8005`). See files for CRC implementation in C++.

### AFS Telemetry Data
```c
struct afsTelemetryData {
    uint8_t type = 0x00;
    uint32_t timestamp;
    uint8_t state;
    int16_t angularVelocityX = 0xFFFF;
    int16_t angularVelocityY = 0xFFFF;
    int16_t angularVelocityZ = 0xFFFF;
    int16_t accelerationX = 0xFFFF;
    int16_t accelerationY = 0xFFFF;
    int16_t accelerationZ = 0xFFFF;
    int16_t magneticFieldX = 0xFFFF;
    int16_t magneticFieldY = 0xFFFF;
    int16_t magneticFieldZ = 0xFFFF;
    int16_t temperature = 0xFFFF;
    int32_t altitude = 0xFFFFFFFF;
    int32_t ecefPositionX = 0xFFFFFFFF;
    int32_t ecefPositionY = 0xFFFFFFFF;
    int32_t ecefPositionZ = 0xFFFFFFFF;
    uint32_t ecefPositionAccuracy = 0xFFFFFFFF;
    int32_t ecefVelocityX = 0xFFFFFFFF;
    int32_t ecefVelocityY = 0xFFFFFFFF;
    int32_t ecefVelocityZ = 0xFFFFFFFF;
    uint32_t ecefVelocityAccuracy = 0xFFFFFFFF;
    uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| state | `unsigned char` | | System state, see `AfsState` struct below |
| angularVelocityX | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `signed short` | $10^{-2}\ \degree C$ |  Magnetic field along z-axis, launch vehicle frame |
| altitude | `signed long` | $cm$ | Mean sea level altitude |
| ecefPositionX | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `unsigned long` | $cm$ | GNSS estimated position accuracy |
| ecefVelocityX | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `unsigned long` | $cm/s$ | GNSS estimated velocity accuracy |
```c
struct AfsState {
    bool armPinState : 1;       // 0: unarmed, 1: armed
    bool drogueContinuity : 1;  // 0: no continuity, 1: continuity
    bool mainContinuity : 1;    // 0: no continuity, 1: continuity
    uint16_t : 1;               // reserved
    /**
     * 0x0: standby
     * 0x1: armed
     * 0x2: boost
     * 0x3: coast
     * 0x4: apogee
     * 0x5: drogue fired
     * 0x6: drogue opened
     * 0x7 drogue failure
     * 0x8: main fired
     * 0x9: main opened
     * 0xA: main failure
     * 0xB: land
     */
    uint8_t state : 4;
};
```
> `timestamp` and `state` fields are required. All other fields are optional and shall remain its default value to indicate no data.

### ECU Telemetry Data
```c
struct ecuTelemetryData {
    uint8_t type = 0x10;
    uint32_t timestamp;
    uint64_t : 8;
    int16_t angularVelocityX = 0xFFFF;
    int16_t angularVelocityY = 0xFFFF;
    int16_t angularVelocityZ = 0xFFFF;
    int16_t accelerationX = 0xFFFF;
    int16_t accelerationY = 0xFFFF;
    int16_t accelerationZ = 0xFFFF;
    int16_t magneticFieldX = 0xFFFF;
    int16_t magneticFieldY = 0xFFFF;
    int16_t magneticFieldZ = 0xFFFF;
    int16_t temperature = 0xFFFF;
    int32_t altitude = 0xFFFFFFFF;
    int32_t ecefPositionX = 0xFFFFFFFF;
    int32_t ecefPositionY = 0xFFFFFFFF;
    int32_t ecefPositionZ = 0xFFFFFFFF;
    uint32_t ecefPositionAccuracy = 0xFFFFFFFF;
    int32_t ecefVelocityX = 0xFFFFFFFF;
    int32_t ecefVelocityY = 0xFFFFFFFF;
    int32_t ecefVelocityZ = 0xFFFFFFFF;
    uint32_t ecefVelocityAccuracy = 0xFFFFFFFF;
    uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| angularVelocityX | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `signed short` | $10^{-2}\ \degree C$ |  Magnetic field along z-axis, launch vehicle frame |
| altitude | `signed long` | $cm$ | Mean sea level altitude |
| ecefPositionX | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `unsigned long` | $cm$ | GNSS estimated position accuracy |
| ecefVelocityX | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `unsigned long` | $cm/s$ | GNSS estimated velocity accuracy |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.

### ECU Fluid System Data
```c
struct ecuFluidSystemData {
  uint8_t type = 0x11;
  uint32_t timestamp = 0xFFFFFFFF;
  uint16_t supplyVoltage = 0xFFFF;
  uint16_t batteryVoltage = 0xFFFF;
  uint64_t : 4;
  bool solenoidStateCopvVent : 1;
  bool solenoidStatePv1 : 1;
  bool solenoidStatePv2 : 1;
  bool solenoidStateVent : 1;
  uint16_t solenoidCurrentCopvVent = 0xFFFF;
  uint16_t solenoidCurrentPv1 = 0xFFFF;
  uint16_t solenoidCurrentPv2 = 0xFFFF;
  uint16_t solenoidCurrentVent = 0xFFFF;
  int16_t temperatureCopv = 0xFFFF;
  uint16_t pressureCopv = 0xFFFF;
  uint16_t pressureLox = 0xFFFF;
  uint16_t pressureLng = 0xFFFF;
  uint16_t pressureInjectorLox = 0xFFFF;
  uint16_t pressureInjectorLng = 0xFFFF;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| supplyVoltage | `unsigned short` | $mV$ | Power supply voltage |
| batteryVoltage | `unsigned short` | $mV$ | Battery supply voltage |
| solenoidStateCopvVent | `bool` | | COPV vent solenoid state |
| solenoidStatePv1 | `bool` | | DLPR Dome fill solenoid state |
| solenoidStatePv2 | `bool` | | DLPR Dome vent solenoid state |
| solenoidStateVent | `bool` | | LOX & LNG vent solenoid state |
| solenoidCurrentCopvVent | `unsigned short` | $mA$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `unsigned short` | $mA$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `unsigned short` | $mA$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `unsigned short` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureCopv | `signed short` | $10^{-2}\ \degree C$ | COPV temperature |
| pressureCopv | `unsigned short` | $10^{-1}\ psi$ | COPV pressure, max range 5000 psi |
| pressureLox | `unsigned short` | $10^{-1}\ psi$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `unsigned short` | $10^{-1}\ psi$ | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | `unsigned short` | $10^{-1}\ psi$ | Injector oxygen pressure, max range 1000 psi |
| pressureInjectorLng | `unsigned short` | $10^{-1}\ psi$ | Injector methane pressure, max range 1000 psi |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.
