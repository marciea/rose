![Current logical network topology](https://github.com/marciea/rose/blob/main/images/logical_network_topo.png?raw=true)
# Home network
>192.168.1.0/24

| Range    | Purpose           |
| -------- | ----------------- |
| `.1`     | Router            |
| `.2-.19` | Machines for Rose |
## Restrictions
> Because my ISP provided router does not support VLAN creation as well as the fact that project rose is in its early stages as of 2026-10-26 19:58, there are no layered protections and the network is considerably flatter than it probably ought to be.
### Current protections:
 - FW rule for blocking LAN  -> WAN (not internet restricting)
	 - This is to prevent any management interfaces being interacted with from the Rose network (e.g. router and switch management page).
 - Separate IoT network 
	 - A symptom of activating the 6GHz band but works great anyway.

> More complex isolation will likely be introduced if my intentions for this home lab necessitate it (e.g. Malware analysis).
# Rose Network

## Rose network categories

| Address/range | Purpose                                        | Example                                          |
| ------------- | ---------------------------------------------- | ------------------------------------------------ |
| `.1`          | Gateway/firewall                               | OPNsense LAN                                     |
| `.2–.9`       | Network infrastructure and existing exceptions | PC at `.2` (for now)                             |
| `.10–.19`     | Dedicated administration devices               | Future lab administration workstation            |
| `.20–.29`     | Hypervisor management                          | Wolves at `.20`; other Proxmox hosts as deployed |
| `.30–.39`     | Shared infrastructure services                 | DNS, reverse proxy, remote-access gateway        |
| `.40–.59`     | Self-hosted applications                       | Forgejo, Wiki.js, dashboards                     |
| `.60–.79`     | Monitoring and security infrastructure         | Monitoring server, log collector, SIEM           |
| `.80–.99`     | Temporary builds and benign experiments        | Test Linux VMs                                   |
| `.100–.199`   | Automatic DHCP pool                            | Ordinary clients and temporary devices           |
| `.200–.254`   | Unallocated expansion                          | Leave unused initially                           |
## Nodes setup
| Network (IP) Address | Hostname    | Hardware                     |
| -------------------- | ----------- | ---------------------------- |
| (Home network) .2    | Bulletproof | Lenovo Ideapad 100s          |
| .2                   | Venom       | Custom PC                    |
| .4                   | Kisses      | ThinkCentre M710q Tiny       |
| .10                  | Halos       | MacBook Pro 16" (M1 Max)     |
| .20                  | Wolves      | Bosgame P4 Plus mini PC      |
| .21                  | Beers       | ThinkCentre M70q Tiny (11DU) |
| .22                  | Oceans      | ThinkCentre 720s (SFF)       |
| .23                  | Doldrums    | Acer Aspire TC-885           |
### Hardware on M710q  |  Kisses  | OPNsense
>The Lenovo M710q Tiny is the lab's firewall and router, running OPNsense on bare metal for no overhead. Both the lack of intention with the particular machine, combined with its resource limitations (for now) are the reason why this is not virtualized. The home router will continue to stay as the household router. OPNsense's WAN side sits on the household network, and the lab networks sit behind it as its LAN.

- i5-7400T, 4 GB RAM , WD SN720 256 GB NVMe.
- One Ethernet port, an Intel I219-V The M.2 2230 Wi-Fi slot is there and the rear punch-outs are present.
- **a second nic is possible and i'm not buying one yet.** i'd only do it if sharing a wire turns out to bother me or cause a problem.
	- [saltyhashes on reddit](https://opnsenselab.com/posts/opnsense-initial-setup-guide/): 
		- "the m710q doesn't have a pci-e slot to add a regular network card like its successor, the m720q, so i had to get a bit creative. turns out there's m.2 a+e intel nics that can replace the wifi card and fit just about perfectly into the accessory port punchout." - https://www.amazon.com/dp/b0bffrxfxh" this is the solution nic that worked for them.
	- [OPNsenseLab Editorial](https://opnsenselab.com/posts/opnsense-initial-setup-guide/): 
		- "prefer Intel NICs (igb/em drivers) over Realtek — the Realtek FreeBSD drivers have historically been less reliable under load"
### "Risks" and "Open items"
- **One NIC means router on a stick.** WAN and the tagged lab VLANs share one cable to the TL-SG108E, so the switch's VLAN settings become part of the security boundary. Every switch change needs a test, and no lab VLAN should reach the switch's management page.
- **4 GB of RAM** is plenty for routing. If I add Suricata or other packages I need to recheck but more than likely it will be a no go on that end.
###  Extra notes
>I want the household to keep working when the lab is off. A separate box means the home router never depends on a lab machine. 

>I picked OPNsense over Pfsense for the amount of community support, the guides, and the available training. Both are FreeBSD so I expect the skills to carry either way. Discussions on Reddit have swayed me in that Pfsense limiting their software to both in regards to it's source as well as slower updates and hardware support.