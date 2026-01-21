# APU Sensors Fix (Ryzen) — Unraid

This repo contains a simple startup script that helps **Unraid + Dynamix System Temp** detect temperature sensors on some **AMD Ryzen APU** systems.

On certain Ryzen APU / mini-PC boards, Unraid boots fine but the **System Temp** plugin shows:

- `Temperature: ... °C` (CPU / Motherboard)
- “No drivers detected”
- or it only shows NVMe/GPU temps until you manually run `modprobe`

This script fixes that by loading the right sensor modules early and giving `hwmon` a moment to populate **before** the System Temp plugin scans.

---

## What the script does

- Loads **Ryzen CPU temperature sensor** module: `k10temp`
- (Optional) Loads iGPU sensor module: `amdgpu` (if present/available)
- Waits briefly so `/sys/class/hwmon/` fills in
- Allows Dynamix System Temp to list sensors like:
  - `k10temp - Tctl` (CPU)
  - `amdgpu - edge` (GPU edge)
  - `nvme - Composite` (often a good “board/case temp” proxy on APU boxes)

---

## Requirements

- Unraid (recommended 7.x)
- **Dynamix System Temp** plugin installed
- **User Scripts** plugin installed (recommended method)

---

## Install (recommended): User Scripts

### 1) Install “User Scripts”
Unraid WebUI → **Apps** → search **User Scripts** → Install.

### 2) Create a new script
Unraid WebUI → **Settings → User Scripts** → **Add New Script**

Name it:
`APU Sensors Fix (Ryzen)`

Paste the contents of this repo’s script into it (or copy it from this file).

### 3) Set it to run at Array Start
In User Scripts, set:

✅ **Schedule**: `At Startup of Array`

This is important — it makes sure the sensors are loaded before Dynamix System Temp tries to detect them.

### 4) Reboot (recommended)
Reboot Unraid once after setting the script.

---

## Configure Dynamix System Temp

After reboot:

1. Unraid WebUI → **Settings → System Temp**
2. Click **Detect**
3. Pick sensors:

### Processor temperature
Select:
- `k10temp - Tctl`

### Mainboard temperature
Many Ryzen APU boxes do not expose a true motherboard sensor.
Best practical choice is usually:
- `nvme - Composite`

(You can also choose another sensor if you prefer, but “Composite” is commonly the most stable.)

4. Click **Apply**

Now the Dashboard should show real temps instead of `... °C`.

---

## Verify in SSH

SSH into Unraid and run:

```bash
sensors
