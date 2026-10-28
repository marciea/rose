## Hosts

| Hostname    | Hardware                     | CPU                     | RAM                             | Drives now                              | Ethernet                           | Physical Address     | Network (IP) Address |
| ----------- | ---------------------------- | ----------------------- | ------------------------------- | --------------------------------------- | ---------------------------------- | -------------------- | -------------------- |
| Bulletproof | Lenovo Ideapad 100s          | Celeron N3060           | 2 GB (soldered)                 | 32 GB eMMC                              | No ethernet available, using Wi-Fi | F0:42:1C:22:7B:DF    | .2                   |
| Kisses      | ThinkCentre M710q Tiny       | i5-7400T (4C/4T)        | 4 GB (1x4 SODIMM)               | WD SN720 256 GB NVMe                    | 1 (Intel I219-V)                   | Needs to be recorded | .4                   |
| Halos       | MacBook Pro 16" (M1 Max)     | Apple M1 Max            | 64 GB (unified)                 | 1 TB                                    | No ethernet available, using Wi-Fi | Not needed           | .10                  |
| Wolves      | Bosgame P4 Plus mini PC      | Ryzen 7 5825U (8C/16T)  | 32 GB (2x16 SODIMM, full)       | Kingston 1 TB NVMe                      | 2 (2.5 GbE reported), Wi-Fi        | 84:47:09:5C:5E:9F    | .20                  |
| Beers       | ThinkCentre M70q Tiny (11DU) | i5-10400T (6C/12T)      | 16 GB (2x8 SODIMM, full)        | Samsung 1 TB NVMe                       | Not recorded                       | 8C:8C:AA:8D:63:BE    | .21                  |
| Oceans      | ThinkCentre 720s (SFF)       | i5-8400 (6C/6T)         | 16 GB (2x8 DIMM)                | WD SN530 512 GB NVMe, 2x Hitachi 500 GB | Not recorded                       | 00:D8:61:68:7D:32    | .22                  |
| Doldrums    | Acer Aspire TC-885           | i3-8100 (4C/4T)         | 8 GB (1x8 DIMM, single channel) | WD SN530 256 GB NVMe, 2x Seagate 500 GB | Not recorded                       | 94:C6:91:96:B9:55    | .23                  |
| Venom       | Custom PC                    | Ryzen 5 5600G + RX 7600 | 32 GB (2x16 DIMM)               | Toshiba 1 TB HDD, SHGP31 1 TB SSD       | Not recorded                       | 10:7C:61:5F:18:75    | .0                   |
## Network gear

| Item                  | What it does                         | Limits                                                             | Status                                                              | Plan                                                                                                                                                              |
| --------------------- | ------------------------------------ | ------------------------------------------------------------------ | ------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Verizon CR1000B       | ISP router, household DHCP/DNS/Wi-Fi | No VLANs. Static routes are supported                              | In use                                                              | Stays as the household router. The household must never depend on lab hardware                                                                                    |
| TP-Link TL-SG108E     | Managed (Easy Smart) 8-port switch   | 802.1Q VLANs, layer 2 only. It can't route or filter between VLANs | In use (nothing configured but all hardware devices are plugged in) | VLAN trunk and access switch for the lab                                                                                                                          |
| TP-Link TL-SG105      | Unmanaged 5-port switch              | No VLANs                                                           | Spare                                                               | Undecided but most likely as a means of a backup (although would require me to disable VLANs completely)                                                          |
| ASUS RT-AX55          | Older Wi-Fi router                   | Works                                                              | Spare                                                               | Undecided but most likely retired as family will not want to sub in this old router, need to see if there are anyways to repurpose this specific model of router. |
| Opnsense on Cork-tree | Firewall and router for the lab      | One Ethernet port                                                  | Planned, not built                                                  | See [[#Opnsense on Kisses]]                                                                                                                                       |
|                       |                                      |                                                                    |                                                                     |                                                                                                                                                                   |
### Current network cabling
![Current network cabling](https://github.com/marciea/rose/blob/main/images/cabling_topo.png?raw=true)

| Switch Port | Device               |
| ----------- | -------------------- |
| 1           | M710q Tiny           |
| 2           | Router               |
| 3           | PC                   |
| 4           | Bosgame P4 Plus Mini |
| 5           | M70Q Tiny            |
| 6           | 720S SFF             |
| 7           | Acer TC-885          |
| 8           | N/A                  |

| VLAN ID | VLAN Name | Member Port | Tagged Ports | Untagged Ports |
| ------- | --------- | ----------- | ------------ | -------------- |
| 10      | Upstream  | 1-2         | 1            | 2              |
| 20      | Rose      | 1,3-8       | 1            | 3,8            |

### Extra notes
- One cable runs from the home router to the 8-port switch.
- Everything except Bulletproof and Halos plugs into that switch. Bulletproof is in the adjacent room, but Halos is where I mainly operate off of in the same room as all the project Rose machines.
- Nothing really "household" is connected to the switch.