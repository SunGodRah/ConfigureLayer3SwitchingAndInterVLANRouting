

# Configure Layer 3 Switching and Inter-VLAN Routing

<p align="center">
  <img src="https://github.com/user-attachments/assets/ea082625-4134-4d9e-91a7-b7ccee5a5688" height="100%" width="100%">
</p>

## Addressing Table
| Device | Interface  | IP Address / Prefix |
|--------|------------|---------------------|
| MLS    | VLAN 10    | 192.168.10.254 /24  |
| MLS    | VLAN 10    | 2001:db8:acad:10::1/64 |
| MLS    | VLAN 20    | 192.168.20.254 /24  |
| MLS    | VLAN 20    | 2001:db8:acad:20::1/64 |
| MLS    | VLAN 30    | 192.168.30.254/24   |
| MLS    | VLAN 30    | 2001:db8:acad:30::1/64 |
| MLS    | VLAN 99    | 192.168.99.254/24   |
| MLS    | G0/2       | 209.165.200.225     |
| MLS    | G0/2       | 2001:db8:acad:a::1/64 |
| PC0    | NIC        | 192.168.10.1        |
| PC1    | NIC        | 192.168.20.1        |
| PC2    | NIC        | 192.168.30.1        |
| PC3    | NIC        | 192.168.10.2/24     |
| PC3    | NIC        | 2001:db8:acad:10::2/64 |
| PC4    | NIC        | 192.168.20.2/24     |
| PC4    | NIC        | 2001:db8:acad:20::2/64 |
| PC5    | NIC        | 192.168.30.2        |
| PC5    | NIC        | 2001:db8:acad:10::2/64 |
| S1     | VLAN 99    | 192.168.99.1        |
| S2     | VLAN 99    | 192.168.99.2        |
| S3     | VLAN 99    | 192.168.99.3        |

## Objectives
1. Configure Layer 3 Switching
2. Configure Inter-VLAN Routing
3. Configure IPv6 Inter-VLAN Routing

## Background / Scenario
A multilayer switch like the Cisco Catalyst 3650 is capable of both Layer 2 switching and Layer 3 routing. One of the advantages of using a multilayer switch is this dual functionality, ideal for small to medium-sized businesses.

---

### Part 1: Configure Layer 3 Switching

First, I need to configure the GigabitEthernet 0/2 port on the switch (MLS) as a routed port and verify connectivity.

1. I open the configuration window on MLS and execute the following commands:

   ```bash
   MLS(config)# interface g0/2
   MLS(config-if)# no switchport
   MLS(config-if)# ip address 209.165.200.225 255.255.255.252
   ```
<p align="center">
  <img src="https://github.com/user-attachments/assets/3f031497-c579-4acf-9d67-6eb6e0427c82" height="70%" width="70%">
</p>

2. To verify, I ping another Layer 3 address:

   ```bash
   MLS# ping 209.165.200.226
   ```

<p align="center">
  <img src="https://github.com/user-attachments/assets/d1e8cb1d-b710-4b9e-823c-8f772c174d7f" height="70%" width="70%">
</p>

The ping was successful, confirming that the Layer 3 interface is configured correctly.

---

### Part 2: Configure Inter-VLAN Routing

Next, I configured inter-VLAN routing to enable communication between different VLANs.

1. First, I added the VLANs to MLS:

   ```bash
   MLS(config)# vlan 10
   MLS(config)# name Staff
   MLS(config)# vlan 20
   MLS(config)# name Student
   MLS(config)# vlan 30
   MLS(config)# name Faculty
   ```

   <p align="center">
  <img src="https://github.com/user-attachments/assets/045cd89e-0d74-4248-a5dc-50867f7f8ffd" height="70%" width="70%">
</p>

2. Then, I configured and activated the SVI interfaces:

   ```bash
   MLS(config)# interface vlan 10
   MLS(config-if)# ip address 192.168.10.254 255.255.255.0
   ```

   <p align="center">
  <img src="https://github.com/user-attachments/assets/a5022992-4b0e-4aa6-b796-feecdf472de8" height="70%" width="70%">
</p>

I repeated this step for VLAN 20, VLAN 30, and VLAN 99, ensuring all interfaces were properly configured.

---

### Part 3: Configure Trunking

Now it was time to configure trunking on the interfaces.

1. I started with interface g0/1 on MLS, making it a static trunk:

   ```bash
   MLS(config-if)# switchport mode trunk
   MLS(config-if)# switchport trunk native vlan 99
   MLS(config-if)# switchport trunk encapsulation dot1q
   ```
  <p align="center">
  <img src="https://github.com/user-attachments/assets/5f5eede5-f7e4-4599-ab48-8a733eae06d2" height="70%" width="70%">
</p>

---

### Part 4: Enable Routing

To enable routing on MLS, I used the `ip routing` command:

```bash
MLS(config)# ip routing
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/efd37f33-de7d-4ade-ba46-294a758548ee" height="70%" width="70%">
</p>

Then, I verified the routing table with the `show ip route` command:

```bash
MLS# show ip route
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/dae69677-50bd-424f-9770-f7abe08619a3" height="70%" width="70%">
</p>

---

### Part 5: Verify End-to-End Connectivity

Finally, I tested connectivity between devices within and across VLANs.

1. From PC0, I pinged PC3 to verify connectivity within VLAN 10.
2. From PC1, I pinged PC4 to verify connectivity within VLAN 20.
3. From PC2, I pinged PC5 to verify connectivity within VLAN 30.


To test inter-VLAN routing, I pinged devices outside the sender's VLAN.

---

### Part 6: Configure IPv6 Inter-VLAN Routing

I also configured IPv6 inter-VLAN routing by enabling IPv6 routing with the following command:

```bash
MLS(config)# ipv6 unicast-routing
```

I then configured IPv6 addressing on SVI for VLANs 10, 20, and 30:

```bash
MLS(config)# interface vlan 10
MLS(config-if)# ipv6 address 2001:db8:acad:10::1/64
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/0b713db9-3035-4507-b223-aef37f121a2b" height="70%" width="70%">
</p>

---

### Part 7: Verify IPv6 Connectivity

After configuring IPv6, I verified inter-VLAN routing and IPv6 connectivity by pinging between devices PC3, PC4, and PC5.



Finally, I pinged the address inside Cloud `2001:db8:acad:a::2` to test connectivity with the external network.

<p align="center">
  <img src="https://github.com/user-attachments/assets/f4dd5d09-558f-4653-a66a-006e4884e5be" height="70%" width="70%">
</p>

---

With all the configurations complete, I successfully set up Layer 3 switching and inter-VLAN routing with both IPv4 and IPv6.
