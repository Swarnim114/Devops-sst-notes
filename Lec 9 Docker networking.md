

---

# 1. IP Addressing (IPv4 & IPv6)

## 1.1 What an IP address represents

* A logical identifier used at the Network layer (OSI Layer 3) to:

  * Identify a host or network interface
  * Provide addressing so routers can forward packets
* Two major protocol versions: **IPv4** (32-bit) and **IPv6** (128-bit).

## 1.2 IPv4 structure

```
32 bits total: 4 octets (8 bits each)
Example: 192.168.10.25 -> 11000000.10101000.00001010.00011001
```

* Notation: dotted-decimal (e.g. `192.168.10.25`)
* Ranges:

  * Public addresses — routable on the Internet
  * Private ranges (RFC1918):

    * `10.0.0.0/8` (10.0.0.0–10.255.255.255)
    * `172.16.0.0/12` (172.16.0.0–172.31.255.255)
    * `192.168.0.0/16` (192.168.0.0–192.168.255.255)

## 1.3 IPv6 basics

* 128-bit, written as eight 16-bit hexadecimal groups separated by `:`.
* Example: `2001:0db8:85a3:0000:0000:8a2e:0370:7334`
* Shortened with `::` for consecutive zeros. IPv6 provides huge address space and built-in features like mandatory IPsec support (original design).

## 1.4 Key IPv4 concepts

* **Network portion vs Host portion** — determined by subnet mask (or prefix `/n`).
* **Broadcast address** — used to send to all hosts in a subnet (IPv4).
* **Network address** — identifies the subnet (all host bits zero).

## 1.5 How IP is used in packets

* IP header contains `src IP` and `dst IP`
* Routers examine destination IP to forward packet
* Layer 2 (Ethernet) handles local delivery; ARP resolves IP -> MAC on local LAN

---

# 2. Ethernet Card (NIC)

## 2.1 What is a NIC?

* Hardware interface between host and Ethernet network.
* Exposes one or more MAC addresses (hardware/physical addresses).
* Types: onboard/integrated, PCIe add-in cards, USB-to-Ethernet adapters.

## 2.2 MAC address

* 48-bit address written as `aa:bb:cc:dd:ee:ff`
* Unique to the NIC vendor+device (first 24 bits OUI vendor id)
* Used at Layer 2 to forward frames on a LAN

## 2.3 NIC features and offloads

* **Auto-negotiation**: speed/duplex detection (10/100/1000/2.5G/10G etc.)
* **Link aggregation**: LACP (802.3ad) for combining multiple links
* **Offloads**: TSO/GSO (segmentation), checksum offload, GRO, RX/TX ring buffers — reduces CPU overhead
* **Promiscuous mode**: receives all frames (useful for packet capture)

## 2.4 NIC drivers & device names (Linux)

* Device names: `eth0`, `enp3s0`, `ens33` (predictable network interface names)
* Commands:

  * `ip link show` — list interfaces
  * `ethtool <ifname>` — query/set link settings

---

# 3. Ethernet Cable & Physical Layer

## 3.1 Types of twisted-pair cables

* **Cat5e** — up to 1 Gbps, typical for home LAN
* **Cat6** — supports 1 Gbps, better for 10 Gbps short runs
* **Cat6a / Cat7 / Cat8** — higher frequency, 10 Gbps+ over longer distances

## 3.2 RJ45 wiring and pinout

* Standards: **T568A** and **T568B** (commonly use B)
* Straight-through vs crossover: modern NICs auto-sense; crossover rarely required

## 3.3 Optical fiber

* Multimode and single-mode fiber for long-distance / high-speed links

## 3.4 Signal characteristics

* Attenuation, crosstalk, shielding (STP vs UTP), max cable length (100m for twisted pair)

---

# 4. Network Switches

## 4.1 Switch vs Hub vs Router

* **Hub**: Layer 1, repeats electrical signal to all ports (obsolete)
* **Switch**: Layer 2, forwards frames to appropriate port using MAC table (CAM table)
* **Router**: Layer 3, forwards packets between different IP subnets

## 4.2 Basic switch operation

* Switch learns MAC addresses by inspecting source MAC of inbound frames
* Maintains a CAM table mapping MAC -> port
* For unknown destination MAC, switches flood frame to all ports (except source)

## 4.3 Advanced features

* **VLANs (802.1Q)** — logical segmentation on a single switch; trunking carries multiple VLANs between switches/routers
* **Spanning Tree Protocol (STP)** — prevents loops by blocking redundant paths; variants: RSTP, MSTP
* **LACP (802.3ad)** — link aggregation for bandwidth and redundancy
* **QoS** — prioritize traffic (DiffServ / 802.1p)
* **Port security** — limit MACs per port

## 4.4 Managed vs Unmanaged

* Unmanaged: plug-and-play, no configuration
* Managed: SNMP, CLI/GUI, VLANs, monitoring, mirroring (SPAN)

---

# 5. Subnetting & Subnet Masks (CIDR & VLSM)

## 5.1 CIDR notation

* `/n` means first `n` bits are network bits.
* Example: `192.168.10.0/23` => netmask `255.255.254.0`

  * Network bits = 23, Host bits = 9
  * Address range: `192.168.10.0` - `192.168.11.255`
  * Total addresses = 2^9 = 512; usable hosts = 510 (excluding network & broadcast)

## 5.2 Calculating subnets (quick method)

1. Convert mask to binary or determine block size of the octet containing the boundary.
2. Determine network, broadcast, first usable, last usable.

### Example `/26`:

* Mask `255.255.255.192` (binary: `11111111.11111111.11111111.11000000`)
* Block size = 64 (256 - 192)
* Subnets of `/26` on `192.168.1.0/24`:

  * `192.168.1.0/26`  (0-63)
  * `192.168.1.64/26` (64-127)
  * `192.168.1.128/26` (128-191)
  * `192.168.1.192/26` (192-255)

## 5.3 VLSM (Variable Length Subnet Mask)

* Allows different subnets to have masks of different lengths, conserving addresses.
* Often used in router addressing and site design.

## 5.4 Supernetting and summarization (CIDR)

* Summarize multiple contiguous networks into a single route, e.g., two `/24` -> one `/23`

## 5.5 Special addresses

* `0.0.0.0` — default route / unspecified
* `127.0.0.1` — loopback
* Broadcast: all host bits = 1 in IPv4 subnet

---

# 6. Routing & "Routespace" (Routing table)

## 6.1 What routing table holds

* Entries mapping destination prefixes to next hop & interface
* Types of routes: **connected**, **static**, **dynamic**, **default**

## 6.2 Typical `ip route` output

```
default via 192.168.1.1 dev eth0 proto dhcp metric 100
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1
```

## 6.3 Route selection

* Longest-prefix match (most specific route wins)
* Metrics tie-break

## 6.4 Common commands

* `ip route show`
* `ip route add <network> via <gateway> dev <if>`
* `ip route replace ...` / `ip route del ...`

## 6.5 Routing protocols (brief)

* **RIP** — distance-vector, small simple networks
* **OSPF** — link-state, internal routing for enterprises
* **BGP** — path-vector, used between ASes on the Internet
* **EIGRP** — Cisco proprietary (hybrid)

## 6.6 NAT and routes

* When NAT/Masquerade is used, routing + iptables rules cooperate to translate source addresses for Internet-bound traffic.

---

# 7. `iptables` (Linux firewall) — deep dive

> `iptables` is the IPv4 packet filter tool using Netfilter hooks. Modern systems may use nftables; `iptables` has a compatibility layer.

## 7.1 Architecture: tables & chains

* Tables: `filter`, `nat`, `mangle`, `raw`, `security`
* Each table has chains; `filter` has `INPUT`, `FORWARD`, `OUTPUT`.
* Chains are ordered lists of rules. Each rule can `ACCEPT`, `DROP`, `REJECT`, `MASQUERADE`, `DNAT`, `SNAT`, `LOG`, etc.

## 7.2 Netfilter hooks

* `PREROUTING` (nat/mangle) — before routing decision
* `INPUT` — packet destined to local host
* `FORWARD` — packet being routed through this host
* `OUTPUT` — locally generated packets
* `POSTROUTING` (nat/mangle) — after routing decision

## 7.3 Stateful filtering (conntrack)

* Use connection tracking states: `-m conntrack --ctstate ESTABLISHED,RELATED` to allow return traffic

## 7.4 Common rules examples

* Allow incoming HTTP:

```bash
sudo iptables -A INPUT -p tcp --dport 80 -m conntrack --ctstate NEW -j ACCEPT
```

* Allow established traffic:

```bash
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
```

* NAT masquerade (typical home gateway):

```bash
sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

* Forward container traffic to host port (DNAT):

```bash
sudo iptables -t nat -A PREROUTING -p tcp --dport 8080 -j DNAT --to-destination 172.17.0.2:80
```

## 7.5 Docker and `iptables`

* Docker manipulates iptables rules automatically when starting containers (unless `--iptables=false`).
* Docker creates `DOCKER` chains in `nat` and `filter` tables and adds NAT rules for port mappings.
* Be cautious: custom firewall rules can break Docker networking if order or FORWARD policy conflicts.

## 7.6 Viewing and saving

* View: `sudo iptables -L -n -v --line-numbers`
* Save/restore: `iptables-save` / `iptables-restore`
* Persist methods vary by distro (`iptables-persistent`, systemd service, `netfilter-persistent`)

## 7.7 Transition: nftables

* Newer distros move to `nftables`. To view full nft ruleset: `sudo nft list ruleset`.

---

# 8. Docker networking (`docker0`, drivers, creating networks)

## 8.1 Default bridge `docker0`

* Docker daemon creates a bridge interface `docker0` and a default subnet (often `172.17.0.0/16`).
* Containers attached to the default bridge get IPs on that subnet.
* Host can access containers via `172.17.x.x` and containers can access host using gateway IP (e.g., `172.17.0.1`).

## 8.2 Network drivers

* **bridge** (default): single-host private network
* **host**: container shares the host network namespace. No isolation for ports
* **none**: no networking; container cannot communicate
* **overlay**: multi-host networks (Docker Swarm, requires key-value store)
* **macvlan**: container appears as a real device on the physical network with its own MAC and IP (useful to bypass NAT)

## 8.3 Creating and inspecting networks

* Create bridge network with custom subnet:

```bash
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --gateway 172.20.0.1 mynet
```

* List networks: `docker network ls`
* Inspect: `docker network inspect mynet`

## 8.4 Static IPs in Docker

```bash
docker run -d --network mynet --ip 172.20.0.10 --name web nginx
```

## 8.5 Docker Compose example (custom network)

```yaml
networks:
  frontend:
    driver: bridge
    ipam:
      driver: default
      config:
        - subnet: 172.21.0.0/24
services:
  app:
    image: nginx
    networks:
      frontend:
        ipv4_address: 172.21.0.10
```

## 8.6 Docker, iptables and forwarding

* Docker needs the kernel ip_forward (`/proc/sys/net/ipv4/ip_forward`) to be `1` for NAT.
* Docker will add MASQUERADE rules to allow containers to reach the Internet.

---

# 9. Common commands & troubleshooting

## 9.1 IP and link utilities

* `ip addr show` — show IP addresses on interfaces
* `ip link show` — show interfaces and state (UP/DOWN)
* `ip route show` — routing table
* `arp -n` or `ip neigh show` — ARP table
* `ethtool <if>` — NIC link, speed, duplex

## 9.2 Packet capture & debug

* `tcpdump -i <if> host 1.2.3.4` — capture packets to/from IP
* `ss -tulpen` — list sockets and listening ports
* `iptables -L -n -v --line-numbers` — check firewall counters

## 9.3 Docker specific

* `docker ps`, `docker inspect <container>`, `docker network ls`, `docker network inspect <net>`
* If container can't access internet: check `iptables -t nat -L -n` and `sysctl net.ipv4.ip_forward`

## 9.4 Useful troubleshooting steps

1. `ip addr` to confirm interface IP
2. `ip route` to confirm gateway/default route
3. `ping <gateway>` to check L2/L3 connectivity
4. `tcpdump -i <if>` while reproducing traffic to see packets
5. `iptables -L -n -v` to verify firewall rules

---

# 10. Quick reference tables

## Binary masks

| CIDR | Mask            | Hosts | Usable hosts |
| ---- | --------------- | ----- | ------------ |
| /32  | 255.255.255.255 | 1     | 1            |
| /31  | 255.255.255.254 | 2     | 0*           |
| /30  | 255.255.255.252 | 4     | 2            |
| /29  | 255.255.255.248 | 8     | 6            |
| /28  | 255.255.255.240 | 16    | 14           |
| /24  | 255.255.255.0   | 256   | 254          |
| /23  | 255.255.254.0   | 512   | 510          |

* `/31` is used for point-to-point links (RFC3021) where two hosts use both addresses.

## Common Docker networks

* `bridge` (default) — single host
* `host` — no network namespace isolation
* `overlay` — multi-host
* `macvlan` — container appears as physical device


