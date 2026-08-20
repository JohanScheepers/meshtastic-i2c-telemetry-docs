# Meshtastic Supported Sensors Reference Guide

A comprehensive technical reference of all supported sensors in the Meshtastic firmware repository (`src/modules/Telemetry/Sensor`), including communication protocols, default addresses, primary measured metrics, and operating ranges.

---

## Table of Contents

* [Overview](#overview)
  * [1. Temperature, Humidity & Atmospheric Pressure](#1-temperature-humidity--atmospheric-pressure)
  * [2. Air Quality & Particulate Matter](#2-air-quality--particulate-matter)
  * [3. Power, Voltage & Battery Monitoring](#3-power-voltage--battery-monitoring)
  * [4. Optical, Light & Radiation](#4-optical-light--radiation)
  * [5. Distance, Weight & Weather Instrumentation](#5-distance-weight--weather-instrumentation)
  * [6. Health & Non-Contact Infrared Temperature](#6-health--non-contact-infrared-temperature)
* [Meshtastic I2C Multi-Device Bus Guide](#meshtastic-i2c-multi-device-bus-guide)
  * [How It Works](#how-it-works)
  * [Connecting Two Identical Sensors](#connecting-two-identical-sensors)
  * [Telemetry Prioritization](#telemetry-prioritization)
* [Meshtastic Sensor JSON Telemetry Payloads](#meshtastic-sensor-json-telemetry-payloads)
  * [Standard Telemetry JSON Envelope](#standard-telemetry-json-envelope)
  * [1. Environmental Telemetry (`environmentMetrics`)](#1-environmental-telemetry-environmentmetrics)
    * [BME280](#bme280)
    * [BME680 / BME688](#bme680--bme688)
    * [BMP280 / BMP085 / BMP180 / BMP388 / DPS310 / LPS22](#bmp280--bmp085--bmp180--bmp388--dps310--lps22)
    * [SHT30 / SHT31 / SHT40 / SHTC3 / AHT10 / AHT20 / DHT11 / DHT22](#sht30--sht31--sht40--shtc3--aht10--aht20--dht11--dht22)
    * [MCP9808 / DS18B20](#mcp9808--ds18b20)
    * [LTR390UV](#ltr390uv)
    * [RCWL9620 (Ultrasonic Distance)](#rcwl9620-ultrasonic-distance)
    * [NAU7802 (Weight / Load Cell)](#nau7802-weight--load-cell)
    * [RadSens (Geiger Counter)](#radsens-geiger-counter)
    * [DFRobot Lark Weather Station](#dfrobot-lark-weather-station)
    * [DFRobot Rain Gauge](#dfrobot-rain-gauge)
  * [2. Air Quality Telemetry (`airQualityMetrics`)](#2-air-quality-telemetry-airqualitymetrics)
    * [SCD30 / SCD40 / SCD41](#scd30--scd40--scd41)
    * [PMSA003I / SPS30](#pmsa003i--sps30)
  * [3. Power Telemetry (`powerMetrics`)](#3-power-telemetry-powermetrics)
    * [INA219 / INA226 / INA260](#ina219--ina226--ina260)
    * [INA3221 (3-Channel Monitor)](#ina3221-3-channel-monitor)
    * [MAX17048 (Fuel Gauge)](#max17048-fuel-gauge)
  * [4. Ambient Lighting Telemetry (`ambientLightingMetrics`)](#4-ambient-lighting-telemetry-ambientlightingmetrics)
    * [OPT3001 / VEML7700 / TSL2591](#opt3001--veml7700--tsl2591)
  * [5. Health Telemetry (`healthMetrics`)](#5-health-telemetry-healthmetrics)
    * [MAX30102](#max30102)

---

## Overview

Meshtastic features an automatic hardware detection mechanism on boot. When sensors are attached to the default I2C bus pins (`SDA` / `SCL`) or configured GPIO pins, the firmware scans for known I2C bus addresses and initializes corresponding telemetry drivers dynamically once enabled in device configuration.

---

### 1. Temperature, Humidity & Atmospheric Pressure

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **BME280** | I2C | `0x76`, `0x77` | Ambient Temperature, Relative Humidity, Barometric Pressure | -40 to +85 °C \| 0 to 100% RH \| 300 to 1100 hPa |
| **BMP280** | I2C | `0x76`, `0x77` | Ambient Temperature, Barometric Pressure | -40 to +85 °C \| 300 to 1100 hPa |
| **BME680 / BME688** | I2C | `0x76`, `0x77` | Temp, Humidity, Pressure, Gas Resistance (IAQ) | -40 to +85 °C \| 0 to 100% RH \| 300 to 1100 hPa \| 0 to 500 IAQ |
| **BMP085 / BMP180** | I2C | `0x76`, `0x77` | Ambient Temperature, Barometric Pressure | -40 to +85 °C \| 300 to 1100 hPa |
| **BMP388 / BMP390** | I2C | `0x76`, `0x77` | Ambient Temperature, High-Precision Pressure | -40 to +85 °C \| 300 to 1250 hPa |
| **DPS310** | I2C | `0x76`, `0x77` | Ambient Temperature, Barometric Pressure | -40 to +85 °C \| 300 to 1200 hPa |
| **LPS22HB / LPS22DF** | I2C | `0x5C`, `0x5D` | Ambient Temperature, Barometric Pressure | -40 to +85 °C \| 260 to 1260 hPa |
| **SHT30 / SHT31 / SHT35** | I2C | `0x44`, `0x45` | High-Accuracy Temp, Relative Humidity | -40 to +125 °C \| 0 to 100% RH |
| **SHT40 / SHT41 / SHT45** | I2C | `0x44`, `0x45` | High-Accuracy Temp, Relative Humidity | -40 to +125 °C \| 0 to 100% RH |
| **SHTC3** | I2C | `0x70` | Ambient Temperature, Relative Humidity | -40 to +125 °C \| 0 to 100% RH |
| **AHT10 / AHT20 / AHT30** | I2C | `0x38` | Ambient Temperature, Relative Humidity | -40 to +85 °C \| 0 to 100% RH |
| **DHT11** | 1-Wire / GPIO | N/A | Ambient Temperature, Relative Humidity | 0 to +50 °C \| 20 to 90% RH |
| **DHT22 / AM2302** | 1-Wire / GPIO | N/A | Ambient Temperature, Relative Humidity | -40 to +80 °C \| 0 to 100% RH |
| **DS18B20** | 1-Wire | N/A | High-Precision Temperature | -55 to +125 °C |
| **MCP9808** | I2C | `0x18` | High-Precision Temperature | -40 to +125 °C |

[↑ Back to Top](#table-of-contents)

---

### 2. Air Quality & Particulate Matter

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **SCD30** | I2C | `0x61` | NDIR CO2 Concentration, Temperature, Humidity | 400 to 10,000 ppm CO2 \| -10 to +60 °C \| 0 to 100% RH |
| **SCD40 / SCD41** | I2C | `0x62` | Photoacoustic CO2, Temperature, Humidity | 400 to 2,000 ppm (SCD40) / 5,000 ppm (SCD41) CO2 |
| **PMSA003I** | I2C | `0x12` | Particulate Matter (PM1.0, PM2.5, PM10) | 0 to 1,000 µg/m³ |
| **SPS30** | I2C | `0x69` | Particulate Matter (PM1.0, PM2.5, PM4.0, PM10) | 0 to 1,000 µg/m³ |

[↑ Back to Top](#table-of-contents)

---

### 3. Power, Voltage & Battery Monitoring

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **INA219** | I2C | `0x40`, `0x41`, `0x44`, `0x45` | Bus Voltage, Current, Power | 0 to 26 V DC \| ±3.2 A (default 0.1 Ω shunt) |
| **INA226** | I2C | `0x40`, `0x41`, `0x44`, `0x45` | Bus Voltage, High-Precision Current, Power | 0 to 36 V DC \| Up to ±8.192 A (shunt dependent) |
| **INA260** | I2C | `0x40`, `0x41`, `0x44`, `0x45` | Bus Voltage, Current, Integrated Power | 0 to 36 V DC \| ±15 A (integrated shunt) |
| **INA3221** | I2C | `0x40`, `0x41`, `0x42`, `0x43` | 3-Channel Bus Voltage & Current | 0 to 26 V DC per channel \| ±1.638 A |
| **MAX17048** | I2C | `0x36` | Single-Cell LiPo Fuel Gauge / State of Charge | 2.5 to 5.0 V DC \| 0 to 100% SoC |

[↑ Back to Top](#table-of-contents)

---

### 4. Optical, Light & Radiation

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **OPT3001** | I2C | `0x44`, `0x45` | Ambient Light Illuminance | 0.01 to 83,865 Lux |
| **VEML7700** | I2C | `0x10` | High-Accuracy Ambient Light, IR Lux | 0 to 120,000 Lux |
| **TSL2591** | I2C | `0x29` | Full-Spectrum Ambient Light | 0.00018 to 88,000 Lux |
| **LTR390UV** | I2C | `0x53` | Ambient Light & Ultraviolet (UV) Index | 0 to 120,000 Lux \| 0 to 11+ UV Index |
| **RadSens** | I2C | `0x66` | Radiation Dosimeter / Geiger-Müller Counter | 0.01 to 1000 µSv/h \| 0 to 65,535 CPM |

[↑ Back to Top](#table-of-contents)

---

### 5. Distance, Weight & Weather Instrumentation

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **RCWL9620** | I2C | `0x57` | Ultrasonic / Doppler Distance | 20 mm to 4,500 mm |
| **NAU7802** | I2C | `0x2A` | Dual-Channel 24-bit ADC / Load Cell Weight | Board/bridge calibration dependent (mV/V scale) |
| **DFRobot Lark** | I2C | `0x42` | Weather Station (Wind Speed, Direction, Temp, Pressure) | Wind: 0–30 m/s \| Dir: 0–360° \| -40 to +85 °C |
| **DFRobot Rain** | I2C | `0x1D` | Tipping Bucket Rainfall Counter | 0.2794 mm rain per tip pulse |

[↑ Back to Top](#table-of-contents)

---

### 6. Health & Non-Contact Infrared Temperature

| Sensor Driver | Interface | Default I2C Address | Primary Metrics | Measurement / Operating Range |
| :--- | :--- | :--- | :--- | :--- |
| **MAX30102**\* | I2C | `0x57` | Pulse Oximetry (SpO2), Heart Rate, Temperature | 30 to 200 BPM \| 70 to 100% SpO2 \| -40 to +85 °C |
| **MLX90614** | I2C | `0x5A` | Non-Contact Infrared Temperature | Object: -70 to +380 °C \| Ambient: -40 to +125 °C |
| **MLX90632** | I2C | `0x3A` | Miniature Infrared Thermometer | Object: -20 to +85 °C \| Ambient: -20 to +85 °C |

*\*Note: Health and biometric sensors require custom firmware builds compiled with health module support enabled.*

[↑ Back to Top](#table-of-contents)

---
## Meshtastic I2C Multi-Device Bus Guide

Meshtastic fully supports running multiple I2C devices on the same I2C bus (`SDA`/`SCL`), provided each device has a unique I2C address.
[↑ Back to Top](#table-of-contents)

---

### How It Works

* **Automatic Bus Discovery:** During boot, Meshtastic executes an I2C bus scan (`ScanI2C`). It probes every standard address on the bus and automatically initializes a telemetry driver for each recognized sensor it finds.
* **Mixed Sensor Setup:** You can chain multiple distinct sensor types on a single set of `SDA`/`SCL` lines without hardware conflict. For example, a single bus can simultaneously run a BME280 (`0x76`), an INA219 power monitor (`0x40`), an OPT3001 light sensor (`0x44`), and an OLED display (`0x3C`).

[↑ Back to Top](#table-of-contents)

---

### Connecting Two Identical Sensors

* **Address Jumpers:** If you connect two identical sensors (such as two BME280s), they will conflict if left on the same default address (`0x76`). You must pull the address pin (`ADDR`/`SDO`) high or solder the hardware jumper on the second sensor to shift its address (e.g., to `0x77`).
* **I2C Multiplexers:** If you need to run multiple identical sensors that have fixed, unchangeable I2C addresses, you can use an I2C multiplexer such as the TCA9548A to isolate the channels.

[↑ Back to Top](#table-of-contents)

---

### Telemetry Prioritization

If multiple connected sensors provide the same physical metric (for example, ambient temperature reported by both a BME280 and an INA219), Meshtastic prioritizes data from dedicated environmental sensors for display and mesh broadcasts.


## Meshtastic Sensor JSON Telemetry Payloads

This document defines the exact JSON structure generated by Meshtastic nodes when telemetry packets are exported via MQTT, Serial, WebSockets, or the REST API. 

In Meshtastic firmware, individual sensors do not transmit custom JSON schemas; instead, sensor drivers populate standardized `Telemetry` Protobuf messages (`Telemetry` `oneof` variant: `environment_metrics`, `power_metrics`, `air_quality_metrics`, `health_metrics`, or `ambient_lighting_metrics`).

[↑ Back to Top](#table-of-contents)

---

### Standard Telemetry JSON Envelope

Every sensor packet sent over MQTT or the API is wrapped in a top-level JSON envelope:

```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "barometric_pressure": 952.900573730469,
        "relative_humidity": 84.7275390625,
        "temperature": 17.7600002288818
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

> **Note on Naming Conventions:** Standard Protobuf JSON serialization uses camelCase keys (e.g., `environmentMetrics`, `barometricPressure`). If consuming raw unmapped dictionary exports, keys may appear in snake_case (`environment_metrics`, `barometric_pressure`).

[↑ Back to Top](#table-of-contents)

---

### 1. Environmental Telemetry (`environmentMetrics`)

Sensors measuring climate, pressure, temperature, humidity, gas, distance, weight, and weather parameters populate the `environmentMetrics` sub-object.

#### BME280
* **Driver Target:** `BME280`
* **Metrics:** Temperature (°C), Relative Humidity (%), Barometric Pressure (hPa)
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "barometric_pressure": 952.900573730469,
        "relative_humidity": 84.7275390625,
        "temperature": 17.7600002288818
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### BME680 / BME688
* **Driver Target:** `BME680`
* **Metrics:** Temperature (°C), Relative Humidity (%), Barometric Pressure (hPa), Gas Resistance (Ω), Indoor Air Quality (IAQ)
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "temperature": 24.10,
        "relativeHumidity": 52.40,
        "barometricPressure": 1012.80,
        "gasResistance": 12500.0,
        "iaq": 45
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### BMP280 / BMP085 / BMP180 / BMP388 / DPS310 / LPS22
* **Driver Target:** `BMP280`, `BMP085`, `BMP3XX`, `DPS310`, `LPS22`
* **Metrics:** Temperature (°C), Barometric Pressure (hPa)
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "temperature": 21.80,
        "barometricPressure": 1014.10
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### SHT30 / SHT31 / SHT40 / SHTC3 / AHT10 / AHT20 / DHT11 / DHT22
* **Driver Target:** `SHT3X`, `SHT4X`, `SHTC3`, `AHT10`, `DHT11`, `DHT22`
* **Metrics:** Temperature (°C), Relative Humidity (%)
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "barometric_pressure": 952.900573730469,
        "relativeHumidity": 84.7275390625,
        "temperature": 17.7600002288818
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### MCP9808 / DS18B20
* **Driver Target:** `MCP9808`, `DS18B20`
* **Metrics:** Temperature (°C)
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "temperature": 17.7600002288818
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### LTR390UV
* **Driver Target:** `LTR390UV`
* **Metrics:** Illuminance (Lux), UV Index
```json
{
    "channel": 0,
    "from": 3769067321,
    "hop_start": 7,
    "hops_away": 0,
    "id": 2895454263,
    "payload": {
        "lux": 1250.0,
        "uvIndex": 3.4
    },
    "rssi": -67,
    "sender": "!938c04bc",
    "snr": 10.5,
    "timestamp": 1787030258,
    "to": 4294967295,
    "type": "telemetry"
}
```

[↑ Back to Top](#table-of-contents)

#### RCWL9620 (Ultrasonic Distance)
* **Driver Target:** `RCWL9620`
* **Metrics:** Distance (mm)
```json
{
  "time": 1718000000,
  "environmentMetrics": {
    "distance": 1450.0
  }
}
```

[↑ Back to Top](#table-of-contents)

#### NAU7802 (Weight / Load Cell)
* **Driver Target:** `NAU7802`
* **Metrics:** Weight (kg or calibrated units)
```json
{
  "time": 1718000000,
  "environmentMetrics": {
    "weight": 12.45
  }
}
```

[↑ Back to Top](#table-of-contents)

#### RadSens (Geiger Counter)
* **Driver Target:** `RadSens`
* **Metrics:** Radiation Level (µSv/h)
```json
{
  "time": 1718000000,
  "environmentMetrics": {
    "radiationLevel": 0.14
  }
}
```

[↑ Back to Top](#table-of-contents)

#### DFRobot Lark Weather Station
* **Driver Target:** `DFRobotLark`
* **Metrics:** Wind Speed (m/s), Wind Direction (°), Temperature (°C), Relative Humidity (%), Barometric Pressure (hPa)
```json
{
  "time": 1718000000,
  "environmentMetrics": {
    "temperature": 19.50,
    "relativeHumidity": 62.00,
    "barometricPressure": 1009.40,
    "windSpeed": 4.20,
    "windDirection": 185
  }
}
```

[↑ Back to Top](#table-of-contents)

#### DFRobot Rain Gauge
* **Driver Target:** `DFRobotRain`
* **Metrics:** Rainfall 1-Hour (mm), Rainfall 24-Hour (mm)
```json
{
  "time": 1718000000,
  "environmentMetrics": {
    "rainfall1h": 0.56,
    "rainfall24h": 3.35
  }
}
```

[↑ Back to Top](#table-of-contents)

---

### 2. Air Quality Telemetry (`airQualityMetrics`)

Sensors measuring CO2 and Particulate Matter populate `airQualityMetrics`. Sensors that measure both climate and air quality (such as SCD30/40/41) transmit climate data via `environmentMetrics` and CO2 via `airQualityMetrics` in separate telemetry updates.

#### SCD30 / SCD40 / SCD41
* **Driver Target:** `SCD30`, `SCD4X`
* **Metrics:** CO2 Concentration (ppm)
```json
{
  "time": 1718000000,
  "airQualityMetrics": {
    "co2": 650
  }
}
```

[↑ Back to Top](#table-of-contents)

#### PMSA003I / SPS30
* **Driver Target:** `PMSA003I`, `SPS30`
* **Metrics:** PM1.0, PM2.5, PM10.0 Concentrations (µg/m³)
```json
{
  "time": 1718000000,
  "airQualityMetrics": {
    "pm10Standard": 12,
    "pm25Standard": 25,
    "pm100Standard": 38,
    "pm10Environmental": 10,
    "pm25Environmental": 22,
    "pm100Environmental": 35
  }
}
```

[↑ Back to Top](#table-of-contents)

---

### 3. Power Telemetry (`powerMetrics`)

Power monitoring ICs populate `powerMetrics` fields.

#### INA219 / INA226 / INA260
* **Driver Target:** `INA219`, `INA226`, `INA260`
* **Metrics:** Channel 1 Bus Voltage (V), Channel 1 Current (mA)
```json
{
  "time": 1718000000,
  "powerMetrics": {
    "ch1Voltage": 12.45,
    "ch1Current": 350.20
  }
}
```

[↑ Back to Top](#table-of-contents)

#### INA3221 (3-Channel Monitor)
* **Driver Target:** `INA3221`
* **Metrics:** Channel 1–3 Voltage (V) and Current (mA)
```json
{
  "time": 1718000000,
  "powerMetrics": {
    "ch1Voltage": 12.40,
    "ch1Current": 250.00,
    "ch2Voltage": 5.05,
    "ch2Current": 120.50,
    "ch3Voltage": 3.31,
    "ch3Current": 45.10
  }
}
```

[↑ Back to Top](#table-of-contents)

#### MAX17048 (Fuel Gauge)
* **Driver Target:** `MAX17048`
* **Metrics:** Voltage (V), Battery Percentage (%)
```json
{
  "time": 1718000000,
  "powerMetrics": {
    "ch1Voltage": 4.12,
    "ch1Current": 0.0
  }
}
```

[↑ Back to Top](#table-of-contents)

---

### 4. Ambient Lighting Telemetry (`ambientLightingMetrics`)

#### OPT3001 / VEML7700 / TSL2591
* **Driver Target:** `OPT3001`, `VEML7700`, `TSL2591`
* **Metrics:** Lux
```json
{
  "time": 1718000000,
  "ambientLightingMetrics": {
    "lux": 450.75
  }
}
```

[↑ Back to Top](#table-of-contents)

---

### 5. Health Telemetry (`healthMetrics`)

#### MAX30102
* **Driver Target:** `MAX30102`
* **Metrics:** Heart Rate (BPM), SpO2 (%), Temperature (°C)
```json
{
  "time": 1718000000,
  "healthMetrics": {
    "heartBpm": 72,
    "spO2": 98,
    "temperature": 36.60
  }
}
```

[↑ Back to Top](#table-of-contents)

A single Telemetry protobuf packet cannot mix metrics across variants (e.g., environmentMetrics and airQualityMetrics are transmitted as distinct variant messages in separate telemetry intervals).

[↑ Back to Top](#table-of-contents)
