---
theme: seriph
# theme: default
title: Learning from BLE Advertisements
class: text-center
highlighter: shiki
drawings:
  persist: false
transition: slide-up
mdc: true
download: true
background: "https://images.unsplash.com/photo-1550009158-9ebf69173e03?w=900&auto=format&fit=crop&q=60&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxzZWFyY2h8MTJ8fGJsdWV0b290aHxlbnwwfHwwfHx8MA%3D%3D"
---

# Learning from BLE Advertisements

It's not machine learning (yet), relax.

Yibo Wei

---
layout: section
---

# Background

---

# What does a BLE device advertise (usually)?

<v-clicks>

- Device name
- Manufacturer data (with a company ID)
- Service/Characteristic UUIDs
- Tx power level (for distance estimation)
- ...
- Many other things that are defined but rarely used

</v-clicks>

---

# When does a BLE device advertise?

<v-clicks>

- To be paired with a central device
  - Wireless earbuds
  - Smartwatches
- To broadcast data to nearby devices
  - Temperature sensors
  - Beacons
  - Apple's FindMy network

</v-clicks>

<v-click>

## Reality:

</v-click>

<v-clicks>

- **When it shouldn't**
- **When programmers are lazy**

</v-clicks>


---

# Example: KARR Alarm System

Don't install this when your dealer tries to sell you one!

<!-- <img src="/QT_example.png" style="height: 80%; margin: 0 auto;"> -->

<LightOrDark>
  <template #dark="props">
    <img src="/QT_dark.png" style="height: 80%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/QT_light.png" style="height: 80%; margin: 0 auto;">
  </template>
</LightOrDark>

---

# Example: MyQ Garage Door Opener

These devices only need to advertise on the first setup... But why are we seeing them all the time?

<LightOrDark>
  <template #dark="props">
    <img src="/MyQ_dark.png" style="height: 80%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/MyQ_light.png" style="height: 80%; margin: 0 auto;">
  </template>
</LightOrDark>

---

# Example: MyQ Garage Door Opener

<img src="/MyQ.jpg" style="height: 80%; margin: 0 auto;">

---

# Example: Govee LED Strip

This is the worst one so far.

<LightOrDark>
  <template #dark="props">
    <img src="/Govee_dark.png" style="height: 80%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/Govee_light.png" style="height: 80%; margin: 0 auto;">
  </template>
</LightOrDark>

---
layout: statement
---

## We want to find more <span v-mark.circle.red>bad</span> devices like these.

---
layout: section
---

# Our work

---

# Data sources

<!-- <img src="/data-sources.png" style="width: 80%; margin: 0 auto;"> -->

<LightOrDark>
  <template #dark="props">
    <img src="/DataSources_dark.png" style="width: 80%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/DataSources_light.png" style="width: 80%; margin: 0 auto;">
  </template>
</LightOrDark>

---
layout: two-cols
---
<img src="/Cluetooth.png" style="height: 50%; margin: 0 auto;">

::right::

# The Cluetooth App

BLE scanner that uploads data to our server

Sample Data:

```json
{
  "mac": "00:11:22:33:44:55",
  "rssi": -50,
  "time": "2024-03-03T12:34:56Z",
  "name": "My Device",
  "manufacturer_id": 12657,
  "lat": 37.7749,
  "lon": -122.4194,
  "accuracy": 10,
  "uuids": ["0000180D-0000-1000-8000-00805F9B34FB"],
  ...
}
```

---

# Extracting UUIDs from APKs

Fully automated APK uuid extraction workflow:

<!-- <img src="/apk_uuid_workflow.png" style="height: 88%; margin: 0 auto;"> -->

<LightOrDark>
  <template #dark="props">
    <img src="/apk_uuid_workflow_dark.png" style="height: 88%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/apk_uuid_workflow_light.png" style="height: 88%; margin: 0 auto;">
  </template>
</LightOrDark>

---

# Assigned Numbers

16-bit UUIDs are assigned by the Bluetooth SIG

````md magic-move
```yaml{all|1,4,7,10,13}
- uuid: 0x1809
  name: Health Thermometer
  id: org.bluetooth.service.health_thermometer
- uuid: 0x180A
  name: Device Information
  id: org.bluetooth.service.device_information
- uuid: 0x180D
  name: Heart Rate
  id: org.bluetooth.service.heart_rate
- uuid: 0x180E
  name: Phone Alert Status
  id: org.bluetooth.service.phone_alert_status
- uuid: 0x180F
  name: Battery
  id: org.bluetooth.service.battery_service
```
```yaml{2,5,8,11,14}
# Assigned 16-bit UUIDs converted to 128-bit UUIDs
- uuid: 00001809-0000-1000-8000-00805F9B34FB
  name: Health Thermometer
  id: org.bluetooth.service.health_thermometer
- uuid: 0000180A-0000-1000-8000-00805F9B34FB
  name: Device Information
  id: org.bluetooth.service.device_information
- uuid: 0000180D-0000-1000-8000-00805F9B34FB
  name: Heart Rate
  id: org.bluetooth.service.heart_rate
- uuid: 0000180E-0000-1000-8000-00805F9B34FB
  name: Phone Alert Status
  id: org.bluetooth.service.phone_alert_status
- uuid: 0000180F-0000-1000-8000-00805F9B34FB
  name: Battery
  id: org.bluetooth.service.battery_service
```
````

More details:

https://bitbucket.org/bluetooth-SIG/public/src/main/assigned_numbers/uuids/

---

# Database

We use UUIDs to associate scanned devices with Android apps.


<LightOrDark>
  <template #dark="props">
    <img src="/db_schema_dark.svg" style="height: 85%; margin: 0 auto;">
  </template>
  <template #light="props">
    <img src="/db_schema_light.svg" style="height: 85%; margin: 0 auto;">
  </template>
</LightOrDark>

---

# Future work

<v-clicks>

- Scan a lot more
- Identify critical infrastructure devices
- Can we connect a device to a Wi-Fi network?
- Machine learning?

</v-clicks>

---
layout: section
---

# Questions?

---

# About this presentation

I hate PowerPoint and Google Slides.

- Slides are made with [Slidev](https://sli.dev/)
- Diagrams are made with [Excalidraw](https://excalidraw.com/)
- The database schema diagram is made with [dbdiagram.io](https://dbdiagram.io/)

---
layout: end
---

# Thank you!
