APU Sensors Fix (Ryzen APU) — Unraid + Dynamix System Temp
This is a simple startup script that ensures Ryzen APU temperature sensors are visible to Dynamix System Temp (and the dashboard), by loading the correct kernel modules on boot/array start.
What it fixes
On some Ryzen APU systems (e.g. 5825U), Dynamix System Temp may show ...°C until sensors are loaded. This script loads:
k10temp → CPU temperature (Tctl)
amdgpu → iGPU temperature (edge) (if supported/loaded)
NVMe temps show automatically via nvme hwmon
Install (Recommended: User Scripts plugin)
1) Install User Scripts
Unraid WebUI → Apps → search User Scripts → Install.
2) Create a new script
Unraid WebUI → Settings → User Scripts
Click Add New Script → name it:
APU Sensors Fix (Ryzen)
3) Paste this code
Copy code
Bash
#!/bin/bash
# APU Sensors Fix for Ryzen APU systems (Unraid)
# Ensures Dynamix System Temp can see: k10temp (CPU Tctl), amdgpu (edge), nvme composite

logger -t apu-sensors "Loading Ryzen APU sensor modules..."

# CPU sensor (Ryzen)
modprobe k10temp 2>/dev/null || true

# iGPU sensor (only if iGPU driver exists/loads)
modprobe amdgpu 2>/dev/null || true

# Let hwmon populate
sleep 2

# Optional: log detected sensors (useful for debugging)
if command -v sensors >/dev/null 2>&1; then
  sensors 2>/dev/null | logger -t apu-sensors
fi

logger -t apu-sensors "Done."
exit 0
4) Set it to run automatically
In User Scripts, set the schedule to:
✅ At Startup of Array
(Works reliably and avoids “...°C” after reboot.)
Configure Dynamix System Temp
Unraid WebUI → Settings → System Temp → click Detect
Then set:
Processor temperature: k10temp - Tctl
Mainboard temperature: nvme - Composite (best “system heat” proxy on many mini-PCs)
Click Apply
Verify (optional)
SSH into Unraid and run:
Copy code
Bash
sensors
You should see entries like:
k10temp-pci-00c3 → Tctl
amdgpu-pci-0400 → edge
nvme-pci-0300 → Composite
