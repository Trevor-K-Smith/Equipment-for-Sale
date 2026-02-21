# Equipment for Sale

This repository is for selling server hardware owned by Trevor K Smith. Each server gets a quick-glance entry in the main README and its own folder with detailed hardware reports.

Contact: smithktrevor@gmail.com

## Adding a New Server

When a new server is added to the project, follow this workflow:

### 1. README Entry (Quick Glance)

Add a new section to `README.md` with a table like the existing servers. The README is the storefront — buyers should see all key specs at a glance without clicking into anything.

Each component row should include:
- The hardware model/specs in a concise format
- A bold health summary where applicable (e.g., **99% life remaining**, **0 ECC errors**, **0 remapped rows**)
- A link to the detailed report in the server's folder

### 2. Server Folder Structure

Create a folder `server-N/` with the following subfolders:

```
server-N/
├── chassis-info/
│   ├── chassis-dmi-report.txt      # dmidecode -t chassis
│   ├── baseboard-dmi-report.txt    # dmidecode -t baseboard (motherboard model, serial)
│   ├── bios-dmi-report.txt         # dmidecode -t bios (BIOS vendor, version, date)
│   └── system-dmi-report.txt       # dmidecode -t system (system manufacturer, product name)
├── cpu-info/
│   ├── cpu-dmi-report.txt          # dmidecode -t processor
│   ├── lscpu-report.txt            # lscpu (detailed CPU topology, flags, extensions)
│   ├── cpu-mce-log.txt             # dmesg MCE errors (Machine Check Exceptions)
│   └── thermal-snapshot.txt        # sensors output (CPU and system thermals)
├── ram-health/
│   ├── ram-dimm-report.txt         # dmidecode -t memory (every DIMM: slot, size, speed, part number, serial)
│   └── ram-ecc-report.txt          # edac-util summary + dmesg ECC errors
├── gpu-health/
│   ├── gpu-N-MODEL.txt             # nvidia-smi -q -i N (one file per GPU: temp, ECC, remapped rows, PCIe errors)
│   └── nvidia-smi-full-report.txt  # nvidia-smi -q (all GPUs combined, full dump)
└── disk-health/
    ├── DRIVE-MODEL-nvme.txt        # smartctl -a /dev/nvmeXnY (SMART health, life %, read/write totals)
    └── DRIVE-MODEL-sata.txt        # smartctl -a /dev/sdX (SMART health, wear leveling, error logs)
```

### 3. What to Collect Per Component

**Chassis / Motherboard:**
- `sudo dmidecode -t chassis` — chassis type, manufacturer, serial
- `sudo dmidecode -t baseboard` — motherboard model, manufacturer, serial
- `sudo dmidecode -t bios` — BIOS vendor, version, release date
- `sudo dmidecode -t system` — system product name, manufacturer, serial
- README shows: chassis model and form factor only

**CPUs:**
- `sudo dmidecode -t processor` — socket, model, core count, speed, voltage
- `lscpu` — full topology, cache sizes, flags, extensions, vulnerabilities
- `sudo dmesg | grep -i mce` — Machine Check Exception errors (should be empty)
- `sensors` — per-CCD thermal readings
- README shows: model, core count, architecture, socket + MCE error count

**RAM:**
- `sudo dmidecode -t memory` — every DIMM slot with size, speed, part number, serial, manufacturer
- `sudo edac-util -s` + `dmesg | grep -i ecc` — ECC error summary
- README shows: total capacity, DIMM count, speed, part number + ECC error count

**GPUs:**
- `nvidia-smi -q -i N` — per-GPU: serial, temp, power, PCIe link, remapped rows, ECC errors
- `nvidia-smi -q` — full dump of all GPUs
- README shows: model, VRAM + remapped rows and PCIe error count

**Storage (NVMe):**
- `sudo smartctl -a /dev/nvmeXnY` — full SMART report
- README shows: model, capacity, life % remaining

**Storage (SATA):**
- `sudo smartctl -a /dev/sdX` — full SMART report
- README shows: model, capacity, life % remaining (from wear leveling count)

### 4. Additional Health Checks to Consider

If the server has any of these, collect them too:
- `sudo ipmitool sensor list` — BMC/IPMI sensor data (fan speeds, voltages, temperatures) if IPMI is available
- `sudo ipmitool sel list` — IPMI System Event Log (hardware alerts/failures)
- `sudo lspci -vvv` — detailed PCIe device tree (useful for verifying link speeds)
- `sudo nvme smart-log /dev/nvmeXnY` — additional NVMe health beyond smartctl

### 5. Focus

This project is about **hardware and hardware specs only**. Don't include OS, software, or configuration details unless it's:
- BIOS/firmware version (that's on the hardware)
- GPU driver version (relevant to verifying the GPU works)
- NVMe/SSD firmware version (from SMART data, already included)

### 6. Ship

After adding a new server, update CHANGELOG.md and VERSION, then commit and push.

## SSH Access to Server Nodes

The servers documented in this repo are accessible via Tailscale for data collection.

**SSH credentials:** user=`trevorksmith`, password=`password`
**Sudo:** `echo 'password' | sudo -S <cmd>`
**SSH command pattern:** `sshpass -p 'password' ssh -o StrictHostKeyChecking=no trevorksmith@<IP>`

### Node-to-Server Mapping

| Node | Tailscale IP | Folder | System |
|------|-------------|--------|--------|
| (local) | — | server-1/ | GIGABYTE G292-Z45 |
| node-1 | 100.64.0.21 | server-2/ | GIGABYTE R282-Z91 |
| node-2 | 100.64.0.22 | server-3/ | GIGABYTE R282-Z91 |
| node-3 | 100.64.0.23 | server-4/ | GIGABYTE R282-Z91 |
| node-4 | 100.64.0.24 | server-5/ | GIGABYTE R282-Z91 |
| node-5 | 100.64.0.25 | server-6/ | GIGABYTE R182-Z92 |
| node-6 | 100.64.0.26 | server-7/ | GIGABYTE R182-Z93 |
| node-7 | 100.64.0.27 | server-8/ | GIGABYTE R182-Z93 |

### Tool Availability by Node

All nodes have dmidecode, lscpu, smartctl, lm-sensors, and edac-utils installed. Additional tool availability:

| Tool | node-1 | node-2 | node-3 | node-4 | node-5 | node-6 | node-7 |
|------|--------|--------|--------|--------|--------|--------|--------|
| nvidia-smi | yes | no | no | no | no | no | no |
| nvme | no | yes | yes | no | no | no | no |
| ipmitool | no | yes | no | no | no | no | no |
