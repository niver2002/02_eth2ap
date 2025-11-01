# Ethernet to Wi-Fi AP Example for ESP32-P4-NANO

This example demonstrates how to use the Waveshare ESP32-P4-NANO board as a bridge between Ethernet and Wi-Fi Access Point, allowing devices connected via Wi-Fi to access the internet through the Ethernet connection.

## Overview

This example is based on the ESP-IDF `eth2ap` example, specifically configured for the Waveshare ESP32-P4-NANO development board. The ESP32-P4-NANO acts as a simple "router" that forwards packets between its Ethernet port and Wi-Fi AP interface without performing any Layer 3 (TCP/IP) operations.

### Key Features

- Forwards packets between Ethernet (WAN) and Wi-Fi AP (LAN)
- No TCP/IP stack initialization required on ESP32
- Works as a transparent bridge at Layer 2
- Supports Ethernet connection via IP101GRI PHY chip
- Creates a Wi-Fi Access Point for client connections

## Hardware

### ESP32-P4-NANO Board Specifications

- **MCU**: ESP32-P4
- **Ethernet PHY**: IP101GRI (10/100 Mbps)
- **Interface**: RMII
- **Connector**: RJ45 with isolation transformer

### GPIO Configuration

The following GPIO pins are used for Ethernet communication:

| Function | GPIO Pin |
|----------|----------|
| MDC      | GPIO 31  |
| MDIO     | GPIO 52  |
| PHY RST  | GPIO 51  |
| PHY ADDR | 1        |

These configurations are pre-set in `sdkconfig.defaults`.

## Prerequisites

- ESP-IDF v5.0 or later
- Waveshare ESP32-P4-NANO board
- Ethernet cable connected to a router/switch with DHCP server
- Device with Wi-Fi capability (e.g., smartphone, laptop)

## How to Build and Flash

### 1. Set up ESP-IDF

Make sure you have ESP-IDF installed and configured. If not, follow the [ESP-IDF Getting Started Guide](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/get-started/index.html).

### 2. Configure the Project

```bash
idf.py menuconfig
```

Navigate to `Example Configuration` to customize:

- **Wi-Fi SSID**: Default is `ESP32-P4-NANO-WiFi`
- **Wi-Fi Password**: Default is `12345678`
- **Wi-Fi Channel**: Default is channel 1
- **Maximum STA connections**: Default is 4

### 3. Build the Project

```bash
idf.py build
```

### 4. Flash to the Board

```bash
idf.py -p PORT flash monitor
```

Replace `PORT` with your serial port (e.g., `/dev/ttyUSB0` on Linux or `COM3` on Windows).

## Usage

### Step 1: Connect Ethernet

Connect the ESP32-P4-NANO board to your router or switch using an Ethernet cable. The router should have DHCP enabled as devices connecting via Wi-Fi will need to obtain IP addresses from the Ethernet network's DHCP server (the ESP32 itself does not run a DHCP server).

### Step 2: Power On

Power on the ESP32-P4-NANO board. The serial monitor will show:

```
I (xxx) eth2ap_example: Ethernet Started
I (xxx) eth2ap_example: Ethernet Link Up
```

### Step 3: Wi-Fi AP Starts

Once Ethernet is connected, the Wi-Fi Access Point will start:

```
I (xxx) wifi: mode : softAP (xx:xx:xx:xx:xx:xx)
```

### Step 4: Connect to Wi-Fi

Connect your device (smartphone, laptop) to the Wi-Fi network:

- **SSID**: `ESP32-P4-NANO-WiFi` (or your configured SSID)
- **Password**: `12345678` (or your configured password)

### Step 5: Access the Internet

Your device should now have internet access through the ESP32-P4-NANO bridge.

## Expected Output

```
I (508) eth2ap_example: Ethernet Started
I (4518) eth2ap_example: Ethernet Link Up
I (4618) wifi: mode : softAP (30:ae:a4:c6:87:5b)
I (10168) wifi: station: xx:xx:xx:xx:xx:xx join, AID=1
I (10258) eth2ap_example: Wi-Fi AP got a station connected
```

## Troubleshooting

### Wi-Fi Sends Packet Failed

If you see `WiFi send packet failed` errors, the issue is likely due to Ethernet processing packets faster than Wi-Fi. To fix this, increase the `FLOW_CONTROL_WIFI_SEND_TIMEOUT_MS` value in `main/ethernet_example_main.c` (line 37). Change the value from 100 to a higher number like 200 or 300 milliseconds.

### Flow Control Queue Issues

If you see `send flow control message failed or timeout`, you need to increase the queue size. Modify the `FLOW_CONTROL_QUEUE_LENGTH` value in `main/ethernet_example_main.c` (line 36) from 40 to a higher number like 60 or 80.

### No DHCP IP Assignment

The ESP32 doesn't run a DHCP server in this example - it acts as a transparent Layer 2 bridge. Devices connecting to the Wi-Fi AP must obtain IP addresses from the DHCP server on the Ethernet network (typically your router). If your router doesn't have DHCP enabled, connected devices will need to use manually assigned static IP addresses in the same subnet as your Ethernet network.

### Ethernet Link Not Up

- Verify the Ethernet cable is properly connected
- Check that your router/switch is powered on
- Ensure the cable is not damaged
- Verify GPIO pin configurations match your board

## Technical Details

### Layer 2 Bridging

This example operates at the MAC layer (Layer 2), making it efficient for simple packet forwarding. The ESP32 doesn't process Layer 3 (IP) or higher protocols, which means:

- No IP routing
- No NAT (Network Address Translation)
- No firewall
- Direct MAC address forwarding

### Flow Control

Since Ethernet typically processes packets faster than Wi-Fi on ESP32, a flow control queue is implemented to prevent packet loss. Packets from Ethernet are queued and sent to Wi-Fi with appropriate delays.

## References

- [Waveshare ESP32-P4-NANO Wiki](https://www.waveshare.com/wiki/ESP32-P4-Nano-StartPage)
- [ESP-IDF Ethernet Driver Guide](https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/network/esp_eth.html)
- [ESP-IDF eth2ap Example](https://github.com/espressif/esp-idf/tree/master/examples/network/eth2ap)

## License

This example is based on ESP-IDF examples and is provided under the same license (Unlicense OR CC0-1.0).
