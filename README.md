# APU Sensors Fix (Ryzen) — Unraid / Dynamix System Temp helper

This is a tiny Unraid boot-time helper that makes Ryzen APU / Ryzen platforms reliably expose temperature sensors to **Dynamix System Temp**.

Some Ryzen APU systems boot with sensors not populated (or Dynamix shows `...°C`) until the correct kernel hwmon modules are loaded. This script forces the relevant sensor modules to load early at boot so `/sys/class/hwmon/` is populated and Dynamix can detect and display temps.

## What it does

On boot it:
- Loads **k10temp** (Ryzen CPU temperature / Tctl)
- Optionally relies on **amdgpu** if your iGPU driver is loaded (GPU edge temperature)
- Allows **nvme** hwmon temps to appear if you have an NVMe drive (Composite/Sensor 1)

After that, you can go to **Settings → System Temp** and select sensors normally.

> Note: Many mini-PC / NUC-style Ryzen boxes do **not** provide a real “motherboard temp” sensor.
> In that case, you can choose **NVMe Composite** as a *proxy* for “system temp” (but it is NVMe temperature, not chipset/VRM).

---

## Requirements

- Unraid with **Dynamix System Temperature** plugin installed
- `lm-sensors` available on Unraid (System Temp uses it)
- Works best on Ryzen / Ryzen APU systems that expose:
  - `k10temp` (CPU)
  - `amdgpu` (GPU)
  - `nvme` (NVMe drive temps)

---

## Install (recommended method)

### 1) Save this script to the USB boot device

Create a folder and copy the script to your flash:

```bash
mkdir -p /boot/config/custom
cp -f "/path/to/APU Sensors Fix (Ryzen)" "/boot/config/custom/apu_sensors_fix.sh"
chmod +x /boot/config/custom/apu_sensors_fix.sh
