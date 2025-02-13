There might be more complex algorithms out there that could do the work better, commonly used for WANs. However, after testing, they are too computationally expensive for mesh networks like Meshtastic. These algorithms often require maintaining large routing tables, frequent updates, or heavy computation for path discovery, which can overwhelm the limited resources (CPU, memory, and bandwidth) available on low-power devices like those used in Meshtastic.


**Our Devices:**

1. **HELTEC LoRa 32 v3.1 (868MHz)** (6 devices)
- **Processor**:
    - **Microcontroller**: ESP32 (Xtensa® 32-bit LX6 microprocessor) with dual-core CPU, running at up to 240 MHz.
    - **RAM**: 520 KB of SRAM.
    - **Flash Storage**: 4MB of external flash storage.
- **Wireless Communication**:
    - **LoRa Module**: Semtech SX1276, operates on the 868 MHz frequency band, suitable for long-range communication (up to 10+ km under optimal conditions).
    - **Wi-Fi and Bluetooth**: Integrated 802.11b/g/n Wi-Fi and Bluetooth Low Energy (BLE) via the ESP32 chipset, allowing for both local and remote communication.
- **Power Supply**:
    - Voltage: 3.3V (via LDO regulator)
    - Battery: 3.7V Li-Po battery (with charging circuit)
    - Current Consumption: Low-power mode for LoRa transmission and active Wi-Fi/Bluetooth mode for communications.
- **GPIO Pins**: 17 GPIO pins with support for various interfaces, including I2C, SPI, UART, ADC, DAC, PWM.
- **Other Features**:
    - **Integrated OLED Display**: A 0.96-inch OLED screen to display information locally.
    - **Power Consumption**: Ultra-low power consumption, optimized for battery-operated, long-range devices.
- **Use Case**: Suitable for long-range, low-data-rate applications, such as mesh networks, remote sensing, and IoT devices.

2. **HELTEC LoRa 32 v3.1 (433MHz) ** (6 Devices)

- **Processor**:
    - Same **ESP32** microcontroller as the 868 MHz version with dual-core CPU, operating at up to 240 MHz.
    - **RAM**: 520 KB of SRAM.
    - **Flash Storage**: 4MB of external flash storage.
- **Wireless Communication**:
    - **LoRa Module**: Semtech SX1278, operates on the 433 MHz frequency band, offering slightly shorter range compared to 868 MHz but still suitable for medium to long-range communication.
    - **Wi-Fi and Bluetooth**: ESP32 chipset supports Wi-Fi (802.11b/g/n) and Bluetooth Low Energy (BLE).
- **Power Supply**:
    - Voltage: 3.3V (via LDO regulator)
    - Battery: 3.7V Li-Po battery (with integrated charging circuit)
    - Low current consumption for energy-efficient communication.
- **GPIO Pins**: 17 GPIO pins for general-purpose use, including I2C, SPI, UART, ADC, DAC, and PWM.
- **Other Features**:
    - **OLED Display**: Similar to the 868 MHz version, it includes a small OLED display.
- **Use Case**: The 433 MHz version is typically used in regions where this frequency is less congested and has a slightly longer range in some cases. It is ideal for medium-range communications in rural or suburban environments.


3. **Lilygo LoRa 868MHz** (8 Devices)

- **Processor**:
    - **Microcontroller**: ESP32 (similar to the HELTEC versions) with a dual-core CPU, running at up to 240 MHz.
    - **RAM**: 520 KB of SRAM.
    - **Flash Storage**: 4MB of flash storage.
- **Wireless Communication**:
    - **LoRa Module**: Semtech SX1276, operating in the 868 MHz frequency band.
    - **Wi-Fi and Bluetooth**: ESP32 provides support for both Wi-Fi (802.11b/g/n) and Bluetooth Low Energy (BLE).
- **Power Supply**:
    - Voltage: 3.3V with LDO regulator for stable power.
    - Battery: 3.7V Li-Po battery (with integrated charging circuit for charging via USB).
- **GPIO Pins**: Multiple I/O pins for various peripherals, including I2C, SPI, UART, and PWM interfaces.
- **Other Features**:
    - **OLED Display**: Typically includes an onboard small OLED screen for displaying information.
    - **Additional Features**: Some versions may include onboard sensors like temperature or humidity sensors depending on the model.
- **Use Case**: The Lilygo LoRa 868 MHz version is suited for medium-to-long range communication and IoT applications, particularly in outdoor settings where LoRa is most effective.

 **General Comparison**:

- **ESP32 Microcontroller**: All three devices use the ESP32 chipset, meaning they share similar processing power, RAM, and storage capabilities.
- **Power**: All three are designed for low-power applications, with Li-Po batteries and power-saving features to extend runtime, especially in mesh networks where frequent transmissions might otherwise drain power.

**Limitations and Considerations**:

- **Memory and Processing Power**: While the ESP32 is a relatively powerful microcontroller for IoT applications, its RAM and flash memory may limit the execution of complex algorithms (e.g., large-scale routing tables, pathfinding algorithms).
- **Battery Life**: Heavy routing algorithms or frequent retransmissions could impact battery life, so careful power management is necessary.