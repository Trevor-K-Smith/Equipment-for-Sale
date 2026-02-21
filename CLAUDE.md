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

**SSH credentials:** user=`$SSH_USER`, password=`$SSH_PASSWORD`
**Sudo:** `echo '$SSH_PASSWORD' | sudo -S <cmd>`
**SSH command pattern:** `sshpass -p '$SSH_PASSWORD' ssh -o StrictHostKeyChecking=no $SSH_USER@<IP>`

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

All nodes have dmidecode, lscpu, smartctl, lm-sensors, edac-utils, and ipmitool installed. Additional tool availability:

| Tool | node-1 | node-2 | node-3 | node-4 | node-5 | node-6 | node-7 |
|------|--------|--------|--------|--------|--------|--------|--------|
| nvidia-smi | yes | no | no | no | no | no | no |
| nvme | no | yes | yes | no | no | no | no |
| sshpass | no | yes | no | no | no | no | no |
| expect | no | yes | no | no | no | no | no |

## Seagate Corvault Access

Three Seagate Corvault 6575 (4U106) RAID enclosures and one JBOF enclosure are connected to node-2 (100.64.0.22) via SAS and accessible over the management network (10.24.1.0/24). They are **not** directly reachable from the local machine — use node-2 as a jump host.

**SSH credentials:** user=`$SSH_USER`, password=`$CV_PASSWORD`
**Firmware:** S100R013
**CLI:** Seagate storage management CLI (interactive SSH session, requires `expect` on node-2)
**Web UI:** HTTPS on each management IP (same credentials)

### Corvault Mapping

| Corvault | Management IP | MAC Address | Midplane Serial | Mode | Disks |
|----------|--------------|-------------|-----------------|------|-------|
| corevault-1 | 10.24.1.100 | 00:c0:ff:64:da:93 | 00C0FF6446DE | RAID ADAPT | 98 (45 in dg01 missing) |
| corevault-2 | 10.24.1.101 | 00:c0:ff:f6:e8:69 | 00C0FF6447AA | RAID ADAPT | 106 |
| corevault-3 | 10.24.1.102 | 00:c0:ff:64:da:94 | 00C0FF6447AE | RAID ADAPT | 106 |

### Hostname Setup

The Corvault hostnames are NOT persistent in `/etc/hosts` on node-2. If they disappear (e.g., after reboot), re-add them:

```bash
# On node-2, with sudo:
echo "10.24.1.100 corevault-1" >> /etc/hosts
echo "10.24.1.101 corevault-2" >> /etc/hosts
echo "10.24.1.102 corevault-3" >> /etc/hosts
```

### SSH CLI via expect (from node-2)

The Corvault CLI is **interactive only** — it does NOT accept commands as SSH arguments. You MUST use an `expect` script. The password contains `!` which causes bash escaping issues, so store it in a file.

**Step 1: Create password file on node-2 (if not present)**
```bash
echo -n '$CV_PASSWORD' > /tmp/cv_pass.txt
```

**Step 2: Create expect script on node-2**
```bash
cat > /tmp/cv_run.exp << 'EXP'
#!/usr/bin/expect -f
log_user 1
set timeout 60
set ip [lindex $argv 0]
set cmd [lindex $argv 1]

spawn ssh -o StrictHostKeyChecking=no $SSH_USER@$ip
expect "assword:" {
    sleep 1
    send "$CV_PASSWORD\r"
}
sleep 3
expect "#"
send "set cli-parameters pager off\r"
sleep 1
expect "#"
send "$cmd\r"
sleep 8
expect {
    "#" { }
    timeout { }
}
send "exit\r"
expect eof
EXP
chmod +x /tmp/cv_run.exp
```

**Step 3: Run a command**
```bash
# From node-2:
expect /tmp/cv_run.exp 10.24.1.100 "show disk-groups"

# From local machine via SSH to node-2:
sshpass -p '$SSH_PASSWORD' ssh -o StrictHostKeyChecking=no $SSH_USER@100.64.0.22 \
  'expect /tmp/cv_run.exp 10.24.1.100 "show disk-groups"'
```

**For commands needing y/n confirmation** (dequarantine, trust, etc.), use a modified expect script that handles the prompt:
```bash
cat > /tmp/cv_confirm.exp << 'EXP'
#!/usr/bin/expect -f
log_user 1
set timeout 60
set ip [lindex $argv 0]
set cmd [lindex $argv 1]

spawn ssh -o StrictHostKeyChecking=no $SSH_USER@$ip
expect "assword:" {
    sleep 1
    send "$CV_PASSWORD\r"
}
sleep 3
expect "#"
send "set cli-parameters pager off\r"
sleep 1
expect "#"
send "$cmd\r"
sleep 3
expect {
    "(y/n)" { send "y\r"; exp_continue }
    "Continue?" { send "y\r"; exp_continue }
    "#" { }
    timeout { }
}
sleep 3
expect {
    "#" { }
    timeout { }
}
send "exit\r"
expect eof
EXP
chmod +x /tmp/cv_confirm.exp
```

### CRITICAL: SSH username is `$SSH_USER`, NOT `manage`

The previous `manage` user no longer works. Always use `$SSH_USER` for both the SSH username and to authenticate to the Corvault CLI. The old scripts that used `manage@<ip>` will fail with "Password rejected".

### Disk Groups and Volumes

Each RAID Corvault has 2 disk groups (dg01, dg02) with 53 disks each (ADAPT 16+2), and 2 volumes per disk group (4 volumes per Corvault, 12 total).

**Current status (as of 2026-02-21):**

| Corvault | DG | Status | Volumes | Notes |
|----------|-----|--------|---------|-------|
| corevault-1 | dg01 | **OFFL** | Volume_0000, Volume_0001 | 8 disks physically removed, unrecoverable |
| corevault-1 | dg02 | FTOL | Volume_0002, Volume_0003 | Initializing ~70% |
| corevault-2 | dg01 | FTDN | Volume_0000, Volume_0001 | 1 faulted disk (slot 0.76), degraded but online |
| corevault-2 | dg02 | FTOL | Volume_0002, Volume_0003 | OK |
| corevault-3 | dg01 | FTOL | Volume_0000, Volume_0001 | OK |
| corevault-3 | dg02 | FTOL | Volume_0002, Volume_0003 | OK |

### SAS Topology (node-2 to Corvaults)

Node-2 has 5 SAS HBAs (Broadcom SAS3008). The SAS connections to Corvaults:

| SAS Host | SAS Address | Connects To | Devices |
|----------|-------------|-------------|---------|
| host16 | 500605b00ea4cdf0 | CV3 (controller A path) + JBOF | 117 (106 JBOF + 5 CV3 + SES) |
| host17 | 500605b00ea4cfd0 | CV3 (controller B path) + CV1 | 10 (5 CV3 + 5 CV1) |
| host18 | 50030480238e1000 | (unknown) | 1 |
| host19 | 500605b00ea4c970 | CV1 (controller A path) + JBOF | 117 (106 JBOF + 5 CV1 + SES) |
| host20 | 500605b00ea4cb50 | CV2 (single path) | 6 (4 CV2 volumes + 2 SES) |

### Multipath Devices (Corvault RAID volumes on node-2)

| Multipath | dm | WWID | Corvault | Volume | ZFS Pool |
|-----------|-----|------|----------|--------|----------|
| mpathdc | dm-107 | 3600c0ff00064dac7eaf42d6501000000 | CV3 dg01 | Volume_0000 | pool2 |
| mpathdd | dm-108 | 3600c0ff00064dac7eaf42d6502000000 | CV3 dg01 | Volume_0001 | pool3 |
| mpathde | dm-109 | 3600c0ff00064da94f2f42d6501000000 | CV3 dg02 | Volume_0002 | pool4 |
| mpathdf | dm-110 | 3600c0ff00064da94f2f42d6502000000 | CV3 dg02 | Volume_0003 | pool5 |
| mpathdi | dm-111 | 3600c0ff00064da9301f52d6501000000 | CV1 dg02 | Volume_0002 | (raw) |
| mpathdj | dm-112 | 3600c0ff00064da9301f52d6502000000 | CV1 dg02 | Volume_0003 | (raw) |
| mpathdk | dm-113 | 3600c0ff00064da90f2f42d6501000000 | CV2 dg01 | Volume_0000 | (raw) |
| mpathdl | dm-114 | 3600c0ff00064da90f3f42d6501000000 | CV2 dg01 | Volume_0001 | (raw) |
| mpathdm | dm-115 | 3600c0ff000f6e869f8f42d6501000000 | CV2 dg02 | Volume_0002 | (raw) |
| mpathdn | dm-116 | 3600c0ff000f6e869f8f42d6502000000 | CV2 dg02 | Volume_0003 | (raw) |

CV1 dg01 volumes (Volume_0000, Volume_0001) show as 0 bytes — disk group is offline and unrecoverable.

### ZFS Pools on node-2

| Pool | Size | Backend | Mount |
|------|------|---------|-------|
| pool1 | 1.66 PB | dRAID2 across 106 JBOF drives | /pool1 |
| pool2 | 412T | CV3 dg01 Volume_0000 | /pool2 |
| pool3 | 412T | CV3 dg01 Volume_0001 | /pool3 |
| pool4 | 412T | CV3 dg02 Volume_0002 | /pool4 |
| pool5 | 412T | CV3 dg02 Volume_0003 | /pool5 |

### Rescanning for New Volumes

If Corvault volumes don't appear on node-2 (after mapping or power cycle):

```bash
# 1. Rescan SCSI bus
for host in /sys/class/scsi_host/host*; do echo "- - -" > "$host/scan"; done

# 2. Wait for device settle
sleep 10

# 3. Add new devices to multipath (if not auto-detected)
multipath -a /dev/sdXX  # for each new block device

# 4. Reconfigure multipath
multipath -r

# 5. Verify
multipath -ll | grep "SEAGATE,6575"
```

### Common Corvault CLI Commands

- `show system` — system overview, health, serial
- `show controllers` — controller status
- `show disks` / `show disks detail` — physical disk info
- `show disk-statistics` — per-disk I/O stats
- `show disk-groups` / `show disk-groups detail` — RAID group status
- `show volumes` — virtual volumes
- `show maps` — volume-to-host mappings
- `show host-groups` — SAS host initiator groups
- `show ports` — SAS port connection status
- `show sensor-status` — temperatures, fans, PSUs
- `show frus` — field-replaceable units
- `show events` / `show events last N` — event log
- `trust enable` + `trust disk-group <name>` — attempt offline DG recovery (same session!)
- `dequarantine disk-group <name>` — remove quarantine (requires y/n confirm)
