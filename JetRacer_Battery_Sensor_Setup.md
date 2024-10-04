
# Battery Sensor Setup with JetRacer and Raspberry Pi 4

This guide explains how to receive battery sensor data from a JetRacer using a Raspberry Pi 4. The process involves connecting a battery voltage/current sensor to the Raspberry Pi and configuring it to read battery data. Here's the step-by-step explanation:

## 1. Hardware Setup

### Materials Required
- **Raspberry Pi 4** (with Raspbian OS or Ubuntu installed)
- **Battery Sensor** (e.g., INA219 sensor or ADS1115 ADC)
- **Battery (LiPo)** or another power source
- **Connecting Wires (Jumper wires)**
- **Breadboard** (optional)
- **JetRacer Chassis**

We will use an INA219 sensor in this example. This sensor communicates over I2C to measure battery voltage and current.

### Wiring Setup (Using INA219 Sensor)
1. **INA219 to Battery**:
   - **V+**: Connect to the positive terminal of the battery.
   - **V-**: Connect to the positive terminal of the load (JetRacer's power input).
   - **GND**: Connect to the ground (common with the battery and Raspberry Pi).

2. **INA219 to Raspberry Pi 4**:
   - **VCC**: Connect to **3.3V** or **5V** on the Raspberry Pi.
   - **GND**: Connect to **GND** on the Raspberry Pi.
   - **SCL**: Connect to **GPIO 3 (SCL)** on the Raspberry Pi.
   - **SDA**: Connect to **GPIO 2 (SDA)** on the Raspberry Pi.

## 2. Software Setup (Configuring the Raspberry Pi)

### a. Enable I2C on Raspberry Pi
To enable I2C on Raspberry Pi:
- Open the terminal and run:
    ```bash
    sudo raspi-config
    ```
- Navigate to `Interfacing Options` and enable **I2C**.
- Reboot the Raspberry Pi:
    ```bash
    sudo reboot
    ```

### b. Install Required Python Libraries
You need the `smbus2` and `Adafruit CircuitPython INA219` libraries to communicate with the INA219 sensor.

To install them:
```bash
sudo apt-get install python3-smbus python3-pip
pip3 install adafruit-circuitpython-ina219
```

## 3. Writing the Python Code

Once the hardware is connected and libraries are installed, write Python code to read data from the sensor.

### Example Python Code:

```python
import time
import board
import busio
from adafruit_ina219 import INA219

# Create I2C bus
i2c_bus = busio.I2C(board.SCL, board.SDA)

# Create an INA219 object
ina219 = INA219(i2c_bus)

# Read battery voltage and current
while True:
    bus_voltage = ina219.bus_voltage  # Voltage on V- (load side)
    shunt_voltage = ina219.shunt_voltage  # Voltage between V+ and V- across the shunt resistor
    current = ina219.current  # Current in mA
    power = ina219.power  # Power in mW

    # Total battery voltage is bus voltage + shunt voltage
    battery_voltage = bus_voltage + (shunt_voltage / 1000)

    print(f"Battery Voltage: {battery_voltage:.2f} V")
    print(f"Current: {current:.2f} mA")
    print(f"Power: {power:.2f} mW")
    
    time.sleep(1)
```

## 4. Running the Code
To run the Python code and start reading battery sensor data:
- Save the code in a file, e.g., `battery_sensor.py`.
- Run it using:
    ```bash
    python3 battery_sensor.py
    ```

You should now see the battery voltage, current, and power values printed in the terminal.

## 5. Integrating with JetRacer
To integrate this into your JetRacer setup, add the code into your main control loop. You can monitor battery voltage and take actions (e.g., stop the car) if the battery voltage falls below a critical level.

---

### Additional Considerations:
- **Different Sensors**: If using a different sensor (like ADS1115), ensure to configure it properly, and use an ADC if the sensor requires analog inputs.
- **Power Supply**: Ensure your sensor and Raspberry Pi are properly powered. Verify voltage levels are compatible.

This guide explains the basic steps to receive battery sensor data from JetRacer with Raspberry Pi 4.
