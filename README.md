🔗 EtherChannel Configuration Using PAgP
📌 Project Overview

This project demonstrates how to configure EtherChannel using PAgP (Port Aggregation Protocol) in Cisco Packet Tracer.

EtherChannel combines multiple physical links between switches into a single logical Port-Channel. In this lab, three physical links are bundled together to provide better bandwidth utilization, redundancy, and improved STP behavior.

🎯 Objectives
🔗 Understand EtherChannel technology
📡 Understand PAgP protocol
⚙️ Configure EtherChannel between switches
🔄 Configure PAgP desirable and auto modes
🌐 Configure Port-Channel interfaces as trunk links
🔍 Verify EtherChannel formation
🌳 Understand the relationship between EtherChannel and STP
🛠️ Troubleshoot EtherChannel configuration issues
🧠 Key Concepts
🔗 EtherChannel

EtherChannel combines multiple physical Ethernet interfaces into one logical interface.

3 Physical Links
      ↓
   EtherChannel
      ↓
1 Logical Port-Channel
📡 PAgP

PAgP (Port Aggregation Protocol) is a Cisco proprietary protocol used to negotiate EtherChannel formation.

PAgP supports two negotiation modes:

Mode	Description
desirable	Actively tries to form EtherChannel
auto	Passively waits for negotiation
🔄 PAgP Compatibility
Side A	Side B	Result
desirable	desirable	✅ Forms
desirable	auto	✅ Forms
auto	auto	❌ Does not form

💡 Note: on creates a static EtherChannel and does not use PAgP negotiation.

🖧 Network Topology
                         ┌─────────────────────┐
                         │  Multilayer Switch  │
                         │      3650-24TT      │
                         │                     │
                         │  Gi1/0/1 - Gi1/0/3  │
                         │  Gi1/0/4 - Gi1/0/6  │
                         └─────────┬───────────┘
                                   │
                    ┌──────────────┴──────────────┐
                    │                             │
              PAgP Group 1                   PAgP Group 2
              3 Physical Links               3 Physical Links
                    │                             │
          ┌─────────▼─────────┐         ┌─────────▼─────────┐
          │      Switch0      │         │      Switch1      │
          │     2960-24TT     │         │     2960-24TT     │
          │                   │         │                   │
          │     Fa0/1 - 3     │         │     Fa0/1 - 3     │
          └───────────────────┘         └───────────────────┘
🔌 Interface Mapping
PAgP Group 1
Switch0                  Multilayer Switch
────────                  ─────────────────
Fa0/1  ─────────────────  Gi1/0/1
Fa0/2  ─────────────────  Gi1/0/2
Fa0/3  ─────────────────  Gi1/0/3

        ↓

   Port-Channel 1
PAgP Group 2
Multilayer Switch         Switch1
─────────────────         ───────
Gi1/0/4  ───────────────  Fa0/1
Gi1/0/5  ───────────────  Fa0/2
Gi1/0/6  ───────────────  Fa0/3

        ↓

   Port-Channel 2
⚙️ Configuration
1️⃣ Configure PAgP Group 1 on Switch0
enable
configure terminal

interface range fa0/1 - 3
channel-group 1 mode desirable
exit

Configure the logical Port-Channel:

interface port-channel 1
switchport mode trunk
exit
2️⃣ Configure PAgP Group 1 on Multilayer Switch
enable
configure terminal

interface range gigabitEthernet 1/0/1 - 3
channel-group 1 mode auto
exit

Configure the logical Port-Channel:

interface port-channel 1
switchport mode trunk
exit
🔄 PAgP Negotiation
Switch0                         Multilayer Switch

desirable  ◄──── PAgP ────►        auto

                ↓

          EtherChannel 1
             ✅ Forms
🔗 Configure PAgP Group 2
3️⃣ Configure Group 2 on Multilayer Switch
interface range gigabitEthernet 1/0/4 - 6
channel-group 2 mode desirable
exit

Configure the Port-Channel:

interface port-channel 2
switchport mode trunk
exit
4️⃣ Configure Group 2 on Switch1
enable
configure terminal

interface range fa0/1 - 3
channel-group 2 mode auto
exit

Configure the Port-Channel:

interface port-channel 2
switchport mode trunk
exit
🔄 PAgP Negotiation
Multilayer Switch                  Switch1

desirable  ◄──── PAgP ────►          auto

                ↓

          EtherChannel 2
             ✅ Forms
🔍 Verification

After configuring EtherChannel, verify the configuration using:

show etherchannel summary

This command displays:

EtherChannel group
Port-Channel number
PAgP protocol
Member interfaces
EtherChannel status
Additional Verification

Check trunk interfaces:

show interfaces trunk

Check the Port-Channel interface:

show interfaces port-channel 1
show interfaces port-channel 2

Check spanning-tree information:

show spanning-tree
📊 Expected Result

A successful configuration should show:

Port-Channel 1 → PAgP → Bundled Interfaces
Port-Channel 2 → PAgP → Bundled Interfaces

The member interfaces should be successfully bundled into their respective EtherChannel groups.

Fa0/1 ─┐
Fa0/2 ─┼──► Port-Channel 1
Fa0/3 ─┘
Gi1/0/4 ─┐
Gi1/0/5 ─┼──► Port-Channel 2
Gi1/0/6 ─┘
🌳 EtherChannel and STP

Without EtherChannel, multiple parallel links between switches can create Layer 2 redundancy.

STP may place some individual links into a blocking state to prevent loops.

With EtherChannel:

Physical Links
  │
  ├── Link 1
  ├── Link 2
  └── Link 3
       ↓
   EtherChannel
       ↓
  Port-Channel
       ↓
      STP

STP treats the bundled connection as one logical link, allowing the physical member links to work together as the EtherChannel.

🛠️ Troubleshooting

If EtherChannel does not form, check:

1. PAgP modes

Make sure the modes are compatible:

desirable + desirable ✅
desirable + auto      ✅
auto + auto           ❌
2. Interface configuration

Member interfaces should have compatible settings.

Check:

show running-config
3. EtherChannel status
show etherchannel summary
4. Trunk configuration
show interfaces trunk
5. Check Port-Channel
show interfaces port-channel 1
💡 Key Takeaways
🔗 EtherChannel combines multiple physical links into one logical link.
📡 PAgP is a Cisco proprietary EtherChannel negotiation protocol.
⚙️ desirable actively negotiates PAgP.
⏳ auto waits for PAgP negotiation.
❌ auto + auto does not form an EtherChannel.
🌐 Port-Channels can be configured as trunk links.
🌳 STP treats an EtherChannel bundle as one logical path.
🚀 EtherChannel improves bandwidth utilization and provides redundancy.
🧰 Tools & Technologies
🖥️ Cisco Packet Tracer
🔀 Cisco Catalyst 2960-24TT
🔀 Cisco Catalyst 3650-24TT Multilayer Switch
🔗 EtherChannel
📡 PAgP
🌐 IEEE 802.1Q Trunking
🌳 Spanning Tree Protocol (STP)
💻 Cisco IOS CLI

🎓 Learning Outcome

Through this lab, I gained practical experience in:

Configuring EtherChannel using PAgP
Understanding PAgP negotiation
Working with desirable and auto modes
Creating Port-Channel interfaces
Configuring trunk links
Verifying EtherChannel status
Understanding EtherChannel with STP
Troubleshooting Layer 2 link aggregation
🚀 Future Improvements
🔐 Configure EtherChannel using LACP
⚖️ Compare PAgP vs LACP
🌐 Configure VLANs across EtherChannel trunks
🔍 Perform EtherChannel failure testing
🌳 Analyze STP behavior before and after EtherChannel
🛡️ Apply additional Layer 2 security configurations
✅ Project Status

Completed — EtherChannel Configuration using PAgP 🎯

Protocol: PAgP
Groups: 2
Physical Links per Group: 3
Logical Links: 2 Port-Channels
Switches: 3
Trunking: Enabled
Verification: show etherchannel summary
