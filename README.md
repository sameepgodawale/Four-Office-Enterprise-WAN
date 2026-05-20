# Four-Office Enterprise WAN

> A fully simulated Wide Area Network connecting four regional offices in a **ring topology** using **RIPv2 dynamic routing**, with HTTP/DNS services hosted in LAN 1, wireless access in each office, and end-to-end reachability verified through a browser-based web test.

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Suggested Project Names](#2-suggested-project-names)
3. [Network Topology](#3-network-topology)
4. [IP Addressing Scheme](#4-ip-addressing-scheme)
5. [Device Inventory](#5-device-inventory)
6. [Routing Protocol — RIPv2](#6-routing-protocol--ripv2)
7. [Services — HTTP & DNS](#7-services--http--dns)
8. [Wireless Configuration](#8-wireless-configuration)
9. [Known Errors & Issues](#9-known-errors--issues)
10. [How to Open and Run the Simulation](#10-how-to-open-and-run-the-simulation)
11. [Testing & Verification](#11-testing--verification)
12. [Learning Objectives](#12-learning-objectives)
13. [File Structure](#13-file-structure)

---

## 1. Project Overview

This Cisco Packet Tracer project simulates a **Wide Area Network (WAN)** that interconnects **four Local Area Networks (LANs)** spread across four geographically separate offices. The WAN backbone uses a **ring topology**, where each router connects to exactly two other routers, forming a closed loop. This provides a single level of path redundancy — if one WAN link fails, traffic can still traverse the ring in the opposite direction (once converged).

Key technologies demonstrated:

- **RIPv2** — classless distance-vector dynamic routing for automatic route propagation
- **Point-to-point WAN links** — `/30` subnets on serial or Ethernet WAN interfaces
- **HTTP Web Server** — hosted in LAN 1, accessible from all offices
- **DNS Server** — maps `www.wanwith4lans.ro` to the web server's IP
- **Wireless access** — each LAN has wireless connectivity for laptops/mobile devices
- **Private IPv4 addressing** — `192.168.x.0/24` per LAN, `10.0.0.0/30` WAN links

---

## 2. Suggested Project Names

Based on the topology, technologies, and purpose of the project, here are recommended names ranging from descriptive to professional:

| # | Name | Style |
|---|------|-------|
| 1 | **Four-Office Enterprise WAN** ✅ *(chosen)* | Business-oriented |
| 2 | **RIPv2 Ring WAN Simulation** | Protocol-focused |
| 3 | **WAN Ring with 4 LANs** | Descriptive |
| 4 | **Ring-Redundant Multi-Site Network** | Technical/formal |
| 5 | **Cisco PT: Multi-Branch WAN with DNS & HTTP** | Feature-complete title |
| 6 | **RIPv2 Hub-Ring Interconnect Lab** | Lab/academic |
| 7 | **Distributed LAN Ring Backbone** | Architecture-focused |
| 8 | **PacketTracer WAN Ring — Branch Connectivity Lab** | Tool + purpose |

**Chosen:** *Four-Office Enterprise WAN* — business-oriented, clearly conveys the scale (four offices) and the enterprise-grade nature of the design.

---

## 3. Network Topology

```
        [LAN 1 — 192.168.10.0/24]
         HTTP Server + DNS Server
               |
             [R1]
            /    \
  10.0.0.0/30    10.0.0.8/30
          /          \
       [R4]          [R2]
          \          /
  10.0.0.12/30  10.0.0.4/30
            \  /
            [R3]
              |
        [LAN 3 — 192.168.30.0/24]

[LAN 4 — 192.168.40.0/24]    [LAN 2 — 192.168.20.0/24]
attached to R4                attached to R2
```

The four routers form a closed ring:

```
R1 ── R2 ── R3 ── R4 ── R1
```

Each router also serves a local LAN. This means every router has:
- One LAN-facing interface (toward its office switch/AP)
- Two WAN-facing interfaces (toward the two neighboring routers in the ring)

---

## 4. IP Addressing Scheme

### LAN Subnets (`/24`)

| LAN | Network Address | Subnet Mask | Default Gateway | Usable Range |
|-----|-----------------|-------------|-----------------|--------------|
| LAN 1 | 192.168.10.0 | 255.255.255.0 | 192.168.10.1 (R1) | .2 – .254 |
| LAN 2 | 192.168.20.0 | 255.255.255.0 | 192.168.20.1 (R2) | .2 – .254 |
| LAN 3 | 192.168.30.0 | 255.255.255.0 | 192.168.30.1 (R3) | .2 – .254 |
| LAN 4 | 192.168.40.0 | 255.255.255.0 | 192.168.40.1 (R4) | .2 – .254 |

### WAN Point-to-Point Links (`/30`)

`/30` subnets provide exactly 2 usable host addresses — one for each end of a point-to-point link. This is the standard practice to avoid wasting IP space on WAN links.

| Link | Network | Router A | Router B | Broadcast |
|------|---------|----------|----------|-----------|
| R1 ↔ R2 | 10.0.0.0/30 | 10.0.0.1 (R1) | 10.0.0.2 (R2) | 10.0.0.3 |
| R2 ↔ R3 | 10.0.0.4/30 | 10.0.0.5 (R2) | 10.0.0.6 (R3) | 10.0.0.7 |
| R3 ↔ R4 | 10.0.0.8/30 | 10.0.0.9 (R3) | 10.0.0.10 (R4) | 10.0.0.11 |
| R4 ↔ R1 | 10.0.0.12/30 | 10.0.0.13 (R4) | 10.0.0.14 (R1) | 10.0.0.15 |

### Service Hosts (LAN 1)

| Device | IP Address | Purpose |
|--------|------------|---------|
| HTTP Web Server | 192.168.10.x | Hosts the website at `www.wanwith4lans.ro` |
| DNS Server | 192.168.10.x | Resolves `www.wanwith4lans.ro` → web server IP |

> **Note:** The exact host IPs (.x) are defined inside the `.pkt` file. Open the simulation in Cisco Packet Tracer to verify the assigned values.

---

## 5. Device Inventory

### Routers (×4)

| Router | LAN Interface | WAN Interface 1 | WAN Interface 2 |
|--------|--------------|-----------------|-----------------|
| R1 | 192.168.10.1 | 10.0.0.1 (→R2) | 10.0.0.14 (→R4) |
| R2 | 192.168.20.1 | 10.0.0.2 (→R1) | 10.0.0.5 (→R3) |
| R3 | 192.168.30.1 | 10.0.0.6 (→R2) | 10.0.0.9 (→R4) |
| R4 | 192.168.40.1 | 10.0.0.10 (→R3) | 10.0.0.13 (→R1) |

Cisco router models in Packet Tracer typically used for this topology: **1841**, **2811**, or **2901**.

### Switches (×4)

One Layer-2 switch per LAN, connecting the local end devices to the router's LAN interface. Models commonly used: **Cisco 2950** or **2960**.

### Access Points / Wireless (×4)

Each LAN includes a wireless access point so that laptops can connect wirelessly. Each AP is connected to the office switch.

### End Devices

| LAN | Wired Devices | Wireless Devices | Servers |
|-----|--------------|-----------------|---------|
| LAN 1 | PC(s) | Laptop(s) | HTTP Web Server, DNS Server |
| LAN 2 | PC(s) | Laptop(s) | — |
| LAN 3 | PC(s) | Laptop(s) | — |
| LAN 4 | PC(s) | Laptop(s) | — |

---

## 6. Routing Protocol — RIPv2

### Why RIPv2?

RIPv2 is a **classless distance-vector** routing protocol. Unlike RIPv1, it supports:
- **VLSM (Variable Length Subnet Masking)** — critical here because LAN (`/24`) and WAN (`/30`) subnets have different prefix lengths
- **Subnet mask advertisements** — each routing update includes the mask, not just the network
- **Multicast updates** — uses `224.0.0.9` instead of broadcast

### Configuration Applied to Each Router

The following RIPv2 configuration block is applied on **all four routers**:

```
router rip
 version 2
 no auto-summary
 network 192.168.x0.0     ! LAN network (x = 1/2/3/4)
 network 10.0.0.0         ! WAN network (covers all /30 links)
```

> `no auto-summary` is **critical**. Without it, RIPv2 defaults to classful summarization, which collapses all `10.0.0.x/30` subnets into a single `10.0.0.0/8` entry. This causes routing loops and black holes in the ring topology.

### Convergence

After the simulation starts, allow **30–60 seconds** for RIPv2 to fully converge. RIP sends updates every **30 seconds** by default. Once converged, each router's routing table should show:

- Its own directly connected networks (C)
- All three remote LAN subnets (R)
- All WAN subnets not directly connected (R)

To verify convergence on a router, open its CLI and type:

```
show ip route
show ip rip database
```

### Hop Count & Metric

RIP uses **hop count** as its sole metric (max 15 hops; 16 = infinity/unreachable). In a 4-router ring:
- Any LAN is at most **2 hops** from any other LAN
- The ring provides **two paths** between any two routers (clockwise and counter-clockwise)
- RIP will install the **lower hop-count path** or both if equal-cost

---

## 7. Services — HTTP & DNS

### HTTP Web Server

Located in **LAN 1**, this server runs Cisco Packet Tracer's built-in HTTP service. It hosts a simple webpage accessible via browser from any device in the network once RIPv2 has converged.

**Configuration steps (inside Packet Tracer):**
1. Click the server → Desktop tab → IP Configuration
2. Assign a static IP within `192.168.10.0/24` (e.g., `192.168.10.10`)
3. Set Default Gateway to `192.168.10.1` (R1's LAN interface)
4. Click Services tab → HTTP → Enable HTTP (and optionally HTTPS)
5. Edit `index.html` if desired

### DNS Server

Also located in **LAN 1**, the DNS server resolves the domain `www.wanwith4lans.ro` to the HTTP server's IP address.

**Configuration steps:**
1. Click the DNS server → Services tab → DNS
2. Enable DNS service
3. Add an **A Record**:
   - Name: `www.wanwith4lans.ro`
   - Address: `192.168.10.10` (or whichever IP the HTTP server was given)
4. On each **end device** that will use DNS, configure the DNS server field to point to the DNS server's IP address

### Accessing the Website

From any laptop or PC in the network:
1. Open Desktop → Web Browser
2. Type `www.wanwith4lans.ro` in the address bar
3. Press Enter

If DNS and RIP are both functioning, the page loads from the HTTP server.

---

## 8. Wireless Configuration

Each LAN has a wireless access point (AP) connected to the office switch. Laptops connect wirelessly.

**Typical Packet Tracer wireless setup:**
1. Click the Access Point → Config tab
2. Set SSID (e.g., `LAN1-WiFi`, `LAN2-WiFi`, etc.)
3. Set authentication to WPA2-PSK and assign a password, or leave open for lab purposes
4. On each laptop: Click laptop → Desktop → PC Wireless
5. Connect to the appropriate SSID
6. The laptop receives its IP either via static assignment or DHCP

> **DHCP Note:** If DHCP is configured on the router's LAN interface (`ip dhcp pool`), laptops will obtain IPs automatically. If not configured, manually assign IPs in the correct subnet.

---

## 9. Known Errors & Issues

Based on analysis of the project structure and the README, the following errors or potential issues have been identified:

### ❶ Missing `no auto-summary` — **Critical**

**Issue:** If `no auto-summary` is omitted from any router's RIP configuration, RIPv2 will summarize the `10.0.0.0/30` WAN links into the classful `10.0.0.0/8` network. This causes **incorrect routing** where routers advertise an overly broad route and WAN reachability breaks or loops.

**Fix:** Verify all four routers have:
```
router rip
 version 2
 no auto-summary
```

---

### ❷ DNS Server Not Configured on End Devices — **Common Oversight**

**Issue:** Even if the DNS server is correctly configured with an A record, end devices that don't point to the DNS server IP will resolve nothing and fall back to bare-IP access only. Browsing to `www.wanwith4lans.ro` returns an error.

**Fix:** On every PC/Laptop that needs DNS resolution, set the DNS Server field in IP Configuration to the DNS server's IP (e.g., `192.168.10.x`).

---

### ❸ RIP Not Advertising LAN Subnets — **Configuration Error**

**Issue:** If a router's `network` statement under RIP only covers the `10.0.0.0` WAN range and omits the local LAN network (e.g., `network 192.168.10.0`), remote routers will never learn the route to that LAN.

**Fix:** Each router must include **both** its LAN subnet and the WAN range in its RIP configuration.

---

### ❹ Passive Interface Missing on LAN Interfaces — **Best Practice Violation**

**Issue:** By default, RIP sends routing updates out of all interfaces listed under `network`, including LAN-facing interfaces. This means RIP update packets flood into the office LAN, wasting bandwidth and exposing routing information to end hosts. This is not a breaking error but is poor network design.

**Fix (recommended):**
```
router rip
 passive-interface GigabitEthernet0/0   ! or whichever is the LAN interface
```
This stops RIP updates from being sent to end devices while still accepting routes from that interface and advertising it.

---

### ❺ No Default Route or NAT — **Scope Limitation**

**Issue:** The network has no connection to the internet, no default route (`ip route 0.0.0.0 0.0.0.0`), and no NAT/PAT. This is expected for an isolated lab but means the `.ro` domain name is purely fictional — DNS resolution only works internally.

**Note:** This is by design for the simulation, not an error.

---

### ❻ Potential RIP Routing Loop Risk — **Topology Consideration**

**Issue:** In a ring of 4 routers with RIPv2, split horizon is enabled by default and prevents most loops. However, if split horizon is disabled (e.g., on frame-relay sub-interfaces, which don't apply here) or if timers are misconfigured, temporary loops can form. On Ethernet WAN links, split horizon is on by default and is not a concern.

**Verdict:** Not an active issue given this uses Ethernet WAN links, but worth verifying with `show ip interface <interface>`.

---

### ❼ Topology PNG Naming Inconsistency — **Minor**

**Issue:** The screenshot file is named `wan-4lans.png` while the `.pkt` file is named `WAN_with_4LANs.pkt`. These names don't match in casing or format, which can cause confusion.

**Fix:** Rename for consistency, for example:
- `wan-ring-4lans.pkt`
- `wan-ring-4lans.png`

---

## 10. How to Open and Run the Simulation

### Prerequisites

- **Cisco Packet Tracer** version 7.x or later (free download from Cisco Networking Academy at [netacad.com](https://www.netacad.com))
- A Cisco Networking Academy account (free registration required)

### Steps

**1. Download the project**

Clone or download the repository:
```bash
git clone https://github.com/sameepgodawale/Four-Office-Enterprise-WAN.git
```
Or download the ZIP and extract it.

**2. Open the file**

Launch Cisco Packet Tracer, then:
- File → Open → Navigate to `WAN_with_4LANs.pkt`
- Or simply double-click the `.pkt` file if Packet Tracer is associated with it

**3. Wait for RIPv2 convergence**

Once the file loads, the simulation enters **Realtime Mode** by default. Orange/red link indicators will appear briefly as RIP converges. Wait **30–90 seconds** until all link lights turn green.

> To speed this up, switch to **Simulation Mode**, advance time manually, then switch back.

**4. Verify routing tables**

Click any router → CLI tab → type:
```
enable
show ip route
```
You should see routes marked `R` (RIP) for all remote subnets.

**5. Test connectivity**

From any PC/Laptop → Desktop → Command Prompt:
```
ping 192.168.10.10    ! ping the HTTP server from a remote LAN
ping www.wanwith4lans.ro  ! if DNS is configured on that device
```

**6. Test the website**

From any PC/Laptop → Desktop → Web Browser:
```
http://www.wanwith4lans.ro
```
The HTTP server's homepage should load.

---

## 11. Testing & Verification

Use the following checklist to verify the simulation is working correctly:

| Test | Command / Method | Expected Result |
|------|-----------------|-----------------|
| Ping between adjacent LANs | `ping 192.168.20.1` from LAN 1 PC | Success (TTL ~253) |
| Ping across the ring | `ping 192.168.40.1` from LAN 2 PC | Success |
| RIP route table | `show ip route` on any router | R entries for all remote subnets |
| RIP updates active | `debug ip rip` on any router | Update messages every 30s |
| DNS resolution | Web Browser → `www.wanwith4lans.ro` | Page loads |
| HTTP access by IP | Web Browser → `http://192.168.10.10` | Page loads |
| WAN link loss simulation | Delete a WAN cable, wait 3 min | Traffic reroutes via other side of ring |

### Useful CLI Commands

```bash
# On any router
show ip route                        # Full routing table
show ip rip database                 # RIP-learned routes
show ip interface brief              # Interface status summary
show running-config                  # Full device config
ping <destination-ip>               # Connectivity test
traceroute <destination-ip>         # Path trace

# RIP-specific
debug ip rip                         # Live RIP update debug (disable after)
no debug all                         # Stop all debug output
show ip protocols                    # RIP version, timers, network statements
```

---

## 12. Learning Objectives

This project is designed to teach and demonstrate the following networking concepts:

- **WAN Ring Topology** — understanding how a ring provides redundancy vs. a linear or star WAN
- **RIPv2 Configuration** — classless routing, `no auto-summary`, network statements
- **Subnetting** — applying `/24` for LANs and `/30` for point-to-point WAN links
- **IP Addressing Design** — private IP ranges (RFC 1918), minimizing waste on WAN links
- **DNS & HTTP Services** — configuring and testing application-layer services in a simulated network
- **Wireless Networking** — connecting devices via access points within a LAN segment
- **Network Convergence** — understanding how dynamic routing protocols discover and propagate routes
- **Troubleshooting** — using `ping`, `traceroute`, `show` commands to verify network state

---


---

## License

This project is provided for educational purposes. Cisco, Packet Tracer, and IOS are trademarks of Cisco Systems, Inc. The `.ro` domain used (`www.wanwith4lans.ro`) is fictional and used solely within the simulation environment.
