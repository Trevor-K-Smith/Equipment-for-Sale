# Changelog

## v0.1.3 — 2026-02-21
- Renumber servers so GPU servers are #7 (RTX A4000) and #8 (5x RTX A5000)
- Add missing RTX A4000 GPU to Server #3 (node-2)
- Add NIC rows (Intel X520-2 10GbE SFP+) and HBA rows (Broadcom SAS3008/SAS3216) to all servers
- Simplify storage sections to chassis + disks only (remove SAS topology, RAID config, disk group status)

## v0.1.2 — 2026-02-21
- Move JBOF and 3 Corvault RAID enclosures out of Server #3 into separate top-level Storage sections
- Each storage enclosure now has its own section with enclosure details, disk summary, and RAID group status
- Remove credentials from CLAUDE.md; move to gitignored .secrets file
- Scrub credentials from all git history via git filter-repo

## v0.1.1 — 2026-02-21
- Add per-disk health reports for all 3 RAID Corvaults (310 files: 98 + 106 + 106)
- Add Corvault system health reports (system info, disk groups, sensors, events, FRUs)
- Update README with proper Corvault model identification, serials, firmware, and health status
- Update CLAUDE.md with comprehensive Corvault access documentation (SSH, expect scripts, SAS topology, multipath mapping, ZFS pools, recovery procedures)
- Remove superseded files (6575-0B-sata.txt, seagate-st18000nm004j-16.4T-sata.txt)
- Document CV1 dg01 as OFFLINE/unrecoverable (8 disks physically removed)

## v0.1.0 — 2026-02-21
- Add 7 new servers (server-2 through server-8) from Tailscale-connected nodes
- Server #2: GIGABYTE R282-Z91, dual EPYC 7742 ES, 512 GB RAM, 1x RTX A4000, 1x NVMe
- Server #3: GIGABYTE R282-Z91, dual EPYC 7742 ES, 2 TB RAM, 2x PM1733a 12.8 TB NVMe, 212x Seagate 18 TB SAS + JBOD enclosures
- Server #4: GIGABYTE R282-Z91, dual EPYC 7742 ES, 1152 GB RAM, 1x PM1733a 12.8 TB NVMe
- Server #5: GIGABYTE R282-Z91, dual EPYC 7742 ES, 2 TB RAM, 3x PM1733a 12.8 TB NVMe
- Server #6: GIGABYTE R182-Z92, dual EPYC 7742 ES, 2 TB RAM, 1x NVMe
- Server #7: GIGABYTE R182-Z93, dual EPYC 7742 ES, 2 TB RAM, 1x NVMe
- Server #8: GIGABYTE R182-Z93, dual EPYC 7742 ES, 2 TB RAM, 1x NVMe
- Full hardware health reports for all servers (chassis, CPU, RAM, GPU, disk SMART)
- Add SSH access section to CLAUDE.md with node-to-server mapping

## v0.0.11 — 2026-02-20
- Add manufacturer datasheets for all components (chassis, CPU, RAM, GPU, NVMe, SATA)
- Link datasheets from README alongside detail reports

## v0.0.10 — 2026-02-20
- Simplify README to quick-glance format: name, model, health summary, single details link per component

## v0.0.9 — 2026-02-20
- Add model/part numbers to all README components (motherboard, BIOS, RAM, GPU board P/N, GPU serials, drive models, firmware versions)

## v0.0.8 — 2026-02-20
- Add CLAUDE.md with full workflow for adding new server listings
- Add chassis detail reports (chassis, motherboard, BIOS, system DMI, IPMI sensors, IPMI event log)
- Add lscpu detailed CPU report
- Link all detailed reports from README (chassis, CPU lscpu, storage life %, GPU/RAM health)

## v0.0.7 — 2026-02-20
- Remove Mellanox ConnectX-7 and 2x NVIDIA L4 from listing (being pulled from server)
- Renumber GPUs to 5x RTX A5000

## v0.0.6 — 2026-02-20
- Add SMART disk health reports for both drives (NVMe + SATA) in server-1/disk-health/
- Link health reports from README storage row

## v0.0.5 — 2026-02-20
- Revert README to clean table format and remove redundant server-specs.md

## v0.0.4 — 2026-02-20
- Update contact email to smithktrevor@gmail.com

## v0.0.3 — 2026-02-20
- Overhaul README with full marketing-style layout, badges, and server specs table

## v0.0.2 — 2026-02-20
- Add project CLAUDE.md describing repo purpose and workflow

## v0.0.1 — 2026-02-20
- Add server #1 specs (GIGABYTE G292-Z45, dual EPYC, 2TB RAM, 7 GPUs) to README
- Add contact info
