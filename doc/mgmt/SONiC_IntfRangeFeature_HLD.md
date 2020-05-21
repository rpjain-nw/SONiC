# Interface Range Specification support
Implement support for Interface range Specification via KLISH CLI in SONiC management framework.

# High Level Design Document
#### Rev 0.1

# Table of Contents
* [List of Tables](#list-of-tables)
* [Revision](#revision)
* [About This Manual](#about-this-manual)
* [Scope](#scope)
* [Definition/Abbreviation](#definitionabbreviation)

# List of Tables
[Table 1: Abbreviations](#table-1-abbreviations)
# Revision
| Rev | Date | Author | Change Description |
|:---:|:----------:|:-----------------------:|----------------------------------------------|
| 0.1 | 05/17/2020 | Tejaswi Goel | Initial version |

# Scope
* Implementation details, syntax, and unit test cases regarding interface range command support via KLISH CLI.
* Interface range specification will not be supported via REST/gNMI.
* Breakout command will not be supported in interface range configuration mode.

# Definition/Abbreviation
NA

# 1 Feature Overview

Interface Range Specification feature support via Klish CLI will allow specification of range of interfaces to which the subsequent commands will be applied. Configuring a range of interfaces will help reduce the time and effort in configuring interfaces.

## 1.1 Requirements

### 1.1.1 Functional Requirements

Provide ability to create/configure/show/delete range of interfaces via SONiC KLISH CLI.

### 1.1.2 Configuration and Management Requirements
* Support for *interface range* command to configure the range of interfaces in both native and alias mode.
* Extend show and delete interface commands to support range of interfaces.

### 1.1.3 Scalability Requirements
NA
### 1.1.4 Warm Boot Requirements
NA
## 1.2 Design Overview
### 1.2.1 Basic Approach

- KLISH XML changes for adding new CLI view for interface range command.
- Use of VAR tag in KLISH XML to cache existing interfaces list.
- Changes in KLISH actioner script to invoke APIs for range of interfaces.
- Use **interface range** command to configure existing interfaces in the range.
- Use **interface range create** command to create non-existing interfaces in the range.

### 1.2.2 Container
This feature is implemented within the Management Framework container.

### 1.2.3 SAI Overview
# 2 Functionality
## 2.1 Target Deployment Use Cases
## 2.2 Functional Description
# 3 Design
## 3.1 Overview
* Creation and configuration of interfaces to be done as a single transaction.
* Transaction for show and delete of interfaces will be per interface.
* Modifying XML file to use correct PTYPE to support number, range, or comma-delimited list of numbers and ranges.
Example: `ETHER_INTERFACE_RANGE` for physical interfaces<br>
```
<PARAM
    name="phy-if-name"
    help="Physical interface range"
    ptype="ETHER_INTERFACE_RANGE"
/>
```
## 3.2 DB Changes
### 3.2.1 CONFIG DB
### 3.2.2 APP DB
### 3.2.3 STATE DB
### 3.2.4 ASIC DB
### 3.2.5 COUNTER DB

## 3.3 Switch State Service Design
### 3.3.1 Orchestration Agent
### 3.3.2 Other Process
## 3.4 SyncD
## 3.5 SAI
## 3.6 User Interface
### 3.6.1 Data Models
NA
### 3.6.2 CLI
#### 3.6.2.1 Interface range commands in native mode

####  3.6.2.1.1 Ethernet interface range:
##### Configure physical interfaces in given range
Syntax: `interface range Ethernet port - port[,port - port...]`
```
sonic(config)# interface range ?
Ethernet         Physical interface range
Vlan             Vlan interface range
PortChannel      PortChannel interface range
create           Create interfaces in range

sonic(config)# interface range Ethernet 1-4,5
sonic(conf-if-range-Eth1-4,5)# ?
channel-group   Configure PortChannel parameters
end             Exit to the exec Mode
exit            Exit from current mode
link            Interface link                   
mac             Configure MAC parameters
mtu             Configure MTU        
nat-zone        NAT Zone               
no              Negate a command or set its defaults
service-policy  Apply ingress or egress policy      
sflow           Interface sFlow settings
shutdown        Disable the interface     
spanning-tree   Configure spanning tree on interface      
storm-control   configure storm-control   
switchport      Configure switchport parameters      
threshold       Configure priority-group/queue threshold on an interface.
udld            UDLD interface configuration subcommands

sonic(conf-if-range-Eth1-4,5)# mtu 9000
sonic(conf-if-range-Eth1-4,5)#
```
####  3.6.2.1.2 Vlan interface range:
###### Configure Vlan interfaces existing in given range
Syntax: `interface range Vlan Vlan-id - Vlan-id[,Vlan-id - Vlan-id...]`
```
sonic(config)# interface range Vlan 1-20
sonic(conf-if-range-Vl-1-20)# ?
end                Exit to the exec Mode
exit               Exit from current mode
link               Interface link             
mac                Configure MAC parameters   
mtu                Configure MTU
nat-zone           NAT Zone   
neigh_suppress     Enable ARP and ND Suppression      
no                 Negate a command or set its defaults
service-policy     Apply ingress or egress policy

sonic(conf-if-range-Vl-1-20)# mtu 9000
sonic(conf-if-range-Vl-1-20)#
```
###### Create Vlan interfaces in given range
Syntax: `interface range create Vlan Vlan-id - Vlan-id[,Vlan-id - Vlan-id...]`
```
sonic(config)# interface range create ?
Vlan             Vlan interface range
PortChannel      PortChannel interface range

sonic(config)# interface range create Vlan 3-4,10,11
sonic(conf-if-range-Vl-3-4,10,11)#
```
###### Delete Vlan interfaces existing in given range
Syntax: `no interface Vlan Vlan-id - Vlan-id[,Vlan-id - Vlan-id...]`
```
sonic(config)# no interface Vlan 1-20
sonic(config)#
```
#### 3.6.2.1.3 PortChannel interface range:
###### Configure PortChannel interfaces existing in given range
Syntax: `interface range PortChannel Po-id - Po-id[,Po-id - Po-id...]`
```
sonic(config)# interface range PortChannel 1-40
sonic(conf-if-range-Po-1-40)# ?
end             Exit to the exec Mode
exit            Exit from current mode
link            Interface link          
mac             Configure MAC parameters   
mclag           Configure MCLAG interface  
mtu             Configure MTU    
nat-zone        NAT Zone         
no              Negate a command or set its defaults
service-policy  Apply ingress or egress policy     
shutdown        Disable the interface              
spanning-tree   Configure spanning tree on port-channel   
switchport      Configure switchport parameters          

sonic(conf-if-range-Po-1-20)# mtu 9000
sonic(conf-if-range-Po-1-20)#
```
###### Create PortChannel interfaces in given range
Syntax: `interface range create PortChannel Po-id -Po-id[,Po-id - Po-id...]`
```
sonic(config)# interface range create ?
Vlan             Vlan interface range
PortChannel      PortChannel interface range

sonic(config)# interface range create PortChannel 3-4,10,11
sonic(conf-if-range-Po-3-4,10,11)#
```
###### Delete PortChannel interfaces existing in given range
Syntax: `no interface range PortChannel Po-id - Po-id[,Po-id - Po-id...]`
```
sonic(config)# no interface Portchannel 1-30
sonic(config)#
```
####  3.6.2.1.4 Show interfaces existing in given range
```
sonic# show interface Ethernet 0-20
sonic# show interface status Ethernet 0-20,40
sonic# show interface counters Ethernet 0-20
```
#### 3.6.2.2 Interface range Commands in alias mode
##### Configuring a Range of Interfaces
```
sonic(config)# interface range e1/2-e1/10
sonic(conf-if-range-Eth1/2-1/10)# mtu 9000
```
#### 3.6.2.3 Debug Commands
#### 3.6.2.4 IS-CLI Compliance
### 3.6.3 REST API Support
Not supported
#### 3.6.3.1 GET

# 4 Flow Diagrams

# 5 Error Handling

# 6 Serviceability and Debug
# 7 Warm Boot Support

# 8 Scalability

# 9 Unit Test
The following test cases will be tested using CLI interface only:
- Testing the creation of range of Vlan or PortChannel interfaces.
- Testing all commands supported in interface range configuration mode for Ethernet/VLan/PortChannel.
- Testing all delete & show commands with range of interfaces as input. 

**TBD:** Expand and add specifc test cases.

#### Automation
Spytest cases will be implemented for new CLI.
