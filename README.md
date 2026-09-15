# 🔗 EtherChannel Configuration Using PAgP

## 📌 Project Overview

This project demonstrates the configuration and implementation of **EtherChannel** using **Port Aggregation Protocol (PAgP)** in **Cisco Packet Tracer**.

The network consists of three Cisco switches connected using multiple physical links. Instead of treating each physical connection as a separate link, multiple physical links are combined together to form a single logical connection called an **EtherChannel**.

The project uses **PAgP (Port Aggregation Protocol)** to dynamically negotiate and establish EtherChannel between the switches.

The project also focuses on **PAgP negotiation modes, channel groups, Port-Channel interfaces, trunk configuration, bandwidth utilization, redundancy, and EtherChannel verification**.

> 🎯 **Project Focus:** EtherChannel + PAgP + Port-Channel + Trunking + Link Redundancy

# 📋 Case Study

A company requires reliable and higher-bandwidth connections between its network switches.

Multiple physical links are available between the switches. If these links are configured individually, **Spanning Tree Protocol (STP)** may block redundant links to prevent Layer 2 loops.

To make better use of the available physical links, the company wants to combine them into a single logical connection using **EtherChannel**.

For this project, **PAgP** is used to negotiate and establish EtherChannel between the switches.

### Requirements

1. 🔗 Multiple physical links must be combined into EtherChannel
2. 📡 PAgP must be used for EtherChannel negotiation
3. 🔀 Multiple switches must be connected using EtherChannel
4. 🌐 EtherChannel connections must operate as trunk links
5. 🛡️ Redundant physical links must provide better network reliability
6. 🚀 Multiple physical links should provide increased bandwidth
7. 🧪 EtherChannel operation must be verified using Cisco IOS commands

# 🎯 Project Objectives

By completing this project, you will learn how to:

* 🔹 Understand the purpose of EtherChannel
* 🔹 Understand how PAgP works
* 🔹 Understand PAgP negotiation modes
* 🔹 Configure PAgP using `desirable` and `auto` modes
* 🔹 Create EtherChannel using channel groups
* 🔹 Configure Port-Channel interfaces
* 🔹 Configure EtherChannel as a trunk
* 🔹 Combine multiple physical interfaces into one logical link
* 🔹 Understand EtherChannel and STP interaction
* 🔹 Improve bandwidth utilization between switches
* 🔹 Provide link redundancy
* 🔹 Verify EtherChannel operation
* 🔹 Troubleshoot EtherChannel configuration issues

# 🏢 Network Design

The network contains three Cisco switches.

Two Cisco 2960 Layer 2 switches are connected to a central Cisco 3650 multilayer switch using multiple physical links.

Each connection contains **3 physical links**, which are bundled together to form an EtherChannel.

```text
                         ┌──────────────────────────┐
                         │      Central Switch      │
                         │      Cisco 3650-24TT     │
                         │                          │
                         │   Port-Channel 1         │
                         │   Port-Channel 2         │
                         └──────────┬───────┬───────┘
                                    │       │
                         Group 1     │       │    Group 2
                                    │       │
                     ┌──────────────┘       └──────────────┐
                     │                                     │
             ┌───────┴────────┐                    ┌───────┴────────┐
             │    Switch0     │                    │    Switch1     │
             │  Cisco 2960    │                    │  Cisco 2960    │
             └────────────────┘                    └────────────────┘
                     ║║║                                  ║║║
                     ║║║                                  ║║║
                  3 Links                              3 Links

                   PAgP                                  PAgP
              EtherChannel                         EtherChannel
```

# 🔗 EtherChannel Concept

EtherChannel combines multiple physical interfaces into a **single logical interface**.

For example, three physical links can be bundled together:

```text
Physical Links

Fa0/1 ─────────────────────── Fa0/1
Fa0/2 ─────────────────────── Fa0/2
Fa0/3 ─────────────────────── Fa0/3
          │
          ▼
     EtherChannel
          │
          ▼
    Port-Channel 1
```

The switches treat the bundled links as one logical connection.

This allows the network to use multiple physical links while reducing the impact of STP blocking individual redundant links.

# 📡 PAgP — Port Aggregation Protocol

**PAgP (Port Aggregation Protocol)** is a **Cisco proprietary protocol** used to negotiate EtherChannel between compatible Cisco devices.

PAgP automatically determines whether the interfaces can form an EtherChannel based on their configuration.

### PAgP Modes

| Mode        | Description                                    |
| ----------- | ---------------------------------------------- |
| `desirable` | Actively attempts to negotiate an EtherChannel |
| `auto`      | Passively waits for PAgP negotiation           |

### PAgP Compatibility

| Switch A    | Switch B    | Result                       |
| ----------- | ----------- | ---------------------------- |
| `desirable` | `desirable` | ✅ EtherChannel forms         |
| `desirable` | `auto`      | ✅ EtherChannel forms         |
| `auto`      | `desirable` | ✅ EtherChannel forms         |
| `auto`      | `auto`      | ❌ EtherChannel does not form |

> 💡 **Key Point:** At least one side must use `desirable` for PAgP negotiation to establish the EtherChannel.

# 🌐 EtherChannel and Trunking

The EtherChannel interfaces in this project are configured as **trunk links**.

Instead of configuring each physical interface individually as a trunk after creating the EtherChannel, the logical **Port-Channel interface** is configured as the trunk.

```cisco
interface port-channel 1
switchport mode trunk
```

The same concept is applied to Port-Channel 2.

```cisco
interface port-channel 2
switchport mode trunk
```

This allows the EtherChannel to carry traffic for multiple VLANs between the switches.

# 🔄 STP Before EtherChannel

Before EtherChannel is configured, multiple physical connections between switches can create a Layer 2 loop.

STP detects the redundant paths and may place some interfaces into a blocking state.

```text
Switch0                         Central Switch

Fa0/1  🟢──────────────────────── Fa0/1
Fa0/2  🟠──────────────────────── Fa0/2
Fa0/3  🟠──────────────────────── Fa0/3

        STP blocks redundant links
```

In this situation, only one physical link may be forwarding traffic while the other links remain blocked.

This prevents loops but does not allow all available physical links to be used for forwarding.

# 🚀 STP After EtherChannel

After EtherChannel is configured, the three physical links are bundled into one logical Port-Channel.

```text
Switch0                         Central Switch

Fa0/1  ══════════════════════════
Fa0/2  ══════════════════════════
Fa0/3  ══════════════════════════
             │
             ▼
       Port-Channel 1
```

STP sees the bundled EtherChannel as a **single logical link** instead of three independent links.

This allows the physical links within the EtherChannel to participate in forwarding as part of the logical channel.

# ⚙️ Configuration Process

The configuration is divided into the following steps:

1. 🖥️ Build the network topology
2. 🔌 Identify the physical interfaces
3. 🌐 Identify the switch-to-switch trunk links
4. 🔗 Configure PAgP channel groups
5. ⚙️ Configure PAgP negotiation modes
6. 🔀 Configure Port-Channel interfaces
7. 🌐 Configure Port-Channels as trunk links
8. 🧪 Verify EtherChannel operation

# 1️⃣ Build the Network Topology

Create the three-switch topology in Cisco Packet Tracer.

The network contains:

* **1 × Cisco 3650 multilayer switch**
* **2 × Cisco 2960 Layer 2 switches**
* **3 physical links** between the central switch and Switch0
* **3 physical links** between the central switch and Switch1

Each set of three physical links will be configured as an EtherChannel.

# 2️⃣ Identify the Physical Interfaces

Identify the interfaces connecting each switch.

For example, on a Layer 2 switch:

```text
Fa0/1
Fa0/2
Fa0/3
```

These three interfaces will be combined into **Channel Group 1**.

The second switch can use another group:

```text
Fa0/1
Fa0/2
Fa0/3
```

These interfaces will be combined into **Channel Group 2**.

On the central multilayer switch, the corresponding GigabitEthernet interfaces are used for the EtherChannel connections.

# 3️⃣ Configure PAgP — Switch0

Switch0 is connected to the central switch using three physical interfaces.

Configure the interfaces as a PAgP EtherChannel using **Channel Group 1**.

```cisco
enable
configure terminal

interface range fa0/1 - 3
channel-group 1 mode desirable
exit
```

The `desirable` mode actively attempts to establish the PAgP EtherChannel.

# 4️⃣ Configure Port-Channel 1

After assigning the physical interfaces to Channel Group 1, configure the logical Port-Channel interface.

```cisco
interface port-channel 1
switchport mode trunk
exit
```

### Configuration Result

```text
Fa0/1 ─┐
Fa0/2 ─┼── Channel Group 1 ── Port-Channel 1
Fa0/3 ─┘
```

Port-Channel 1 now operates as a trunk.

# 5️⃣ Configure PAgP — Switch1

Switch1 uses three physical interfaces to create **Channel Group 2**.

Configure PAgP using `auto` mode.

```cisco
enable
configure terminal

interface range fa0/1 - 3
channel-group 2 mode auto
exit
```

The `auto` mode waits for the neighboring switch to initiate PAgP negotiation.

# 6️⃣ Configure Port-Channel 2

Configure the logical Port-Channel interface as a trunk.

```cisco
interface port-channel 2
switchport mode trunk
exit
```

### Configuration Result

```text
Fa0/1 ─┐
Fa0/2 ─┼── Channel Group 2 ── Port-Channel 2
Fa0/3 ─┘
```

# 7️⃣ Configure the Central Multilayer Switch

The central Cisco 3650 switch connects to both Layer 2 switches.

Two separate EtherChannels are configured:

* **Port-Channel 1 → Switch0**
* **Port-Channel 2 → Switch1**

## 🔹 Port-Channel 1

Configure the three interfaces connected to Switch0.

```cisco
enable
configure terminal

interface range gigabitEthernet 1/0/1 - 3
channel-group 1 mode auto
exit
```

Configure Port-Channel 1 as a trunk.

```cisco
interface port-channel 1
switchport mode trunk
exit
```

## 🔹 Port-Channel 2

Configure the three interfaces connected to Switch1.

```cisco
interface range gigabitEthernet 1/0/4 - 6
channel-group 2 mode desirable
exit
```

Configure Port-Channel 2 as a trunk.

```cisco
interface port-channel 2
switchport mode trunk
exit
```

# 🔀 Final PAgP Configuration

The final PAgP negotiation relationships are:

```text
                    Central Switch
                    Cisco 3650
                  ┌───────────────┐
                  │               │
          Po1     │               │     Po2
        ──────────┤               ├──────────
                  │               │
                  └───────┬───────┘
                          │
              ┌───────────┴───────────┐
              │                       │
          Switch0                  Switch1
          Cisco 2960               Cisco 2960

       Desirable  ◄──► Auto     Auto  ◄──► Desirable

              PAgP                    PAgP
           Channel 1               Channel 2
```

# 📊 Channel Group Mapping

| Connection               | Physical Links | Channel Group | Port-Channel | PAgP Mode        |
| ------------------------ | -------------: | ------------: | -----------: | ---------------- |
| Switch0 ↔ Central Switch |              3 |             1 |          Po1 | Desirable ↔ Auto |
| Switch1 ↔ Central Switch |              3 |             2 |          Po2 | Auto ↔ Desirable |

This creates two independent EtherChannel connections.

# 🧪 Verification

After completing the configuration, verify the EtherChannel operation using Cisco IOS commands.

## 🔍 Verify EtherChannel Summary

```cisco
show etherchannel summary
```

This command displays:

* EtherChannel group number
* Port-Channel number
* Protocol
* Member interfaces
* Channel status

Expected protocol:

```text
PAgP
```

Example:

```text
Group  Port-channel  Protocol
-----  -------------  --------
1      Po1(SU)        PAgP
2      Po2(SU)        PAgP
```

## 🔍 Verify Trunk Configuration

```cisco
show interfaces trunk
```

This command verifies that the Port-Channel interfaces are operating as trunk links.

## 🔍 Verify Port-Channel Interface

```cisco
show interfaces port-channel 1
```

For the second EtherChannel:

```cisco
show interfaces port-channel 2
```

## 🔍 Verify Interface Status

```cisco
show interfaces status
```

This can be used to check the status of the physical interfaces participating in the EtherChannel.

## 🔍 Verify Running Configuration

```cisco
show running-config
```

This allows the configured EtherChannel, Port-Channel, and trunk settings to be reviewed.

# 🛠️ Troubleshooting

If the EtherChannel does not form correctly, check the following:

### 1. 🔗 Check PAgP Modes

Make sure the two sides use a compatible combination.

```text
Desirable + Desirable → ✅
Desirable + Auto      → ✅
Auto + Auto           → ❌
```

### 2. 🔌 Check Physical Interfaces

Make sure the correct interfaces are included in the channel group.

```cisco
interface range fa0/1 - 3
```

### 3. 🔢 Check Channel Group Numbers

Make sure the correct interfaces are assigned to the intended channel group.

```cisco
channel-group 1 mode desirable
```

### 4. 🌐 Check Trunk Configuration

Verify that the Port-Channel is configured as a trunk.

```cisco
interface port-channel 1
switchport mode trunk
```

### 5. 🧪 Check EtherChannel Status

Use:

```cisco
show etherchannel summary
```

Confirm that the member interfaces are successfully bundled.

# 📈 Benefits Achieved

After implementing EtherChannel, the network achieves:

| Feature          | Before EtherChannel               | After EtherChannel                 |
| ---------------- | --------------------------------- | ---------------------------------- |
| Physical links   | Separate                          | Bundled                            |
| Logical links    | Multiple                          | Single Port-Channel                |
| STP handling     | Individual links                  | Logical EtherChannel               |
| Link utilization | Limited by STP blocking           | Multiple links can participate     |
| Redundancy       | Available but may be blocked      | Available within EtherChannel      |
| Bandwidth        | Limited to active forwarding link | Increased through link aggregation |
| Management       | Multiple individual interfaces    | Logical Port-Channel               |

> 🚀 EtherChannel provides both **link aggregation and redundancy** while allowing multiple physical links to operate as one logical connection.

# 🧠 Key Concepts

### 🔗 EtherChannel

Combines multiple physical interfaces into a single logical connection.

### 📡 PAgP

Cisco proprietary protocol used to negotiate EtherChannel.

### 🔄 Desirable

Actively attempts to establish a PAgP EtherChannel.

### ⏳ Auto

Passively waits for PAgP negotiation.

### 🔀 Channel Group

Logical grouping used to combine physical interfaces.

### 🌐 Port-Channel

The logical interface created by EtherChannel.

### 🚪 Trunk

Allows multiple VLANs to travel across the EtherChannel connection.

### 🛡️ STP

Prevents Layer 2 loops and treats an EtherChannel as a single logical path.

# 📝 Important Commands

```cisco
enable
configure terminal

interface range fa0/1 - 3
channel-group 1 mode desirable
exit

interface port-channel 1
switchport mode trunk
exit

show etherchannel summary
show interfaces trunk
show interfaces port-channel 1
show interfaces status
show running-config
```

# 🎯 Project Outcome

The project successfully demonstrates how multiple physical switch-to-switch links can be combined into logical EtherChannel connections using **PAgP**.

Two EtherChannel groups were configured using three physical links per connection.

```text
3 Physical Links
       ↓
   Channel Group
       ↓
   PAgP Negotiation
       ↓
   Port-Channel
       ↓
    Trunk Link
       ↓
Improved Bandwidth
+ Link Redundancy
```

The configuration was verified using Cisco IOS commands, confirming the operation of **PAgP, Port-Channels, and trunk links**.

# 🚀 Conclusion

This project provides practical experience in configuring **EtherChannel using PAgP** in Cisco Packet Tracer.

By combining multiple physical interfaces into logical Port-Channels, the network can make better use of available links while maintaining redundancy.

The project also demonstrates the relationship between **EtherChannel, PAgP, STP, channel groups, Port-Channels, and trunking**, which are important concepts in Cisco switching and enterprise network design.

# 📚 Technologies & Tools

* 🖥️ Cisco Packet Tracer
* 🔀 Cisco Switching
* 🔗 EtherChannel
* 📡 PAgP
* 🌐 VLAN Trunking
* 🛡️ Spanning Tree Protocol
* 💻 Cisco IOS CLI

# 🏷️ Tags

`#Networking` `#Cisco` `#CCNA` `#EtherChannel` `#PAgP` `#Switching` `#PortChannel` `#Trunking` `#STP` `#CiscoPacketTracer`
