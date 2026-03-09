# Rocket 2 Avionics System

## Overview
WAITING ON SYSTEM DIAGRAM

## Subsystems

### Engine Control Unit (ECU v2.0)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-ecu-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-ecu-firmware

### Ground Support Electronics (GSE v2.1)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-gse-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-gse-firmware

### Telemetry Radio System (TRS)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-trs-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-trs-firmware

### Ground Web Service & Graphical User Interface
New GUI/Backend: https://github.com/UCI-Rocket-Project/VIVIIan \
Deprecated GUI/Backend: https://github.com/UCI-Rocket-Project/rocket2-webservice-gui

## Embedded Firmware Contributing Guidelines
1. Follow all items in Software Contributing Guidelines.
2. Use [PlatformIO](https://platformio.org/).


## Ground Network
All ground communication will be over TCP/IP Ethernet and use the standardized packet format sent via raw bytes. All packets are packed little-endian.\
Checksums are CRC-32 (polynomial `0x04C11DB7`). See files for CRC implementation in C++, this CRC is consistent with Python `binascii.crc32()`. This additional layer of checksum is added to ensure data integrity over embedded serial busses.\
Subnet `10.0.0.1/16`. DHCP Range `10.0.255.0/24`. All other IPs reserved.

### GSE
IP: `10.0.2.0`.\
Open ports `10001-10002` for commands and data. See below for packet format.

### ECU
IP: `10.0.2.1`.\
Open ports `10001-10002` for commands and data. See below for packet format.

### Notes on Packing and Transmission
- Data types are organized little-endian in memory.
- Packets are sent as raw bytes, least significant *byte* first, most significant *bit* first (unless superseded by protocol convention).

A sample transmission sequence is as follows:
```c
void SendData(DataType *data) {
    for (int i = 0; i < sizeof(DataType); i++) {
        SendByte(*((uint8_t *)data + i));
    }
}

void SendByte(uint8_t byte) {
    for (int i = 7; i >= 0; i--) {
        SendBit((byte & (1 << i)) >> i);
    }
}
```

A sample struct like so,
```c
struct DataType {
    uint8_t a = 0xAA;
    uint16_t b = 0x00BB;
};
```
would be sent as follows (leftmost bit first, spaces added for readability): `1010 1010 1011 1011 0000 0000`
