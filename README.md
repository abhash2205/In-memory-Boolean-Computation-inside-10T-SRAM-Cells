# In-memory-Boolean-Computation-inside-10T-SRAM-Cells
## Contents
[Abstract](#abstract)<br>
[Keywords](#keywords)<br>
[Tools Used](#tools-used)<br>
1. [Introduction](#1-introduction)<br>
2. [In Memory Computations In 8T SRAM Cells](#2-in-memory-computations-in-8t-sram-cells)<br>
   - 2.1. [In-Memory NOR/NAND Operation](#21-in-memory-nornand-operation)<br>
3. [Simulation Results](#3-simulation-results)<br>
   - 3.1. [Netlist](#31-netlist)<br>
      - 3.1.1. [NAND netlist](#311-nand-netlist)<br>
      - 3.1.2. [NOR netlist](#312-nor-netlist)<br>
   - 3.2. [Initial Conditions](#32-initial-conditions)<br>
   - 3.3. [Reference Waveforms](#33-reference-waveforms)<br>
   - 3.4. [Simulated Waveforms](#34-simulated-waveforms)<br>
      - 3.4.1. [NAND output](#341-nand-output)<br>
      - 3.4.2. [NOR output](#342-nor-output)<br>
4. [Acknowledgement](#4-acknowledgement)<br>
5. [References](#5-references)<br>
## Abstract
The von Neumann computing architecture has been the workhorse for virtually all the computing systems for last several decades. However, it faces serious issues of memory wall problem with the ever increasing demand of data intensive computing systems. To mitigate this bottleneck, one of the approach that researchers have come up is to enable in-memory Boolean computation. In this work, we will present the circuits and underlying principle behind in-memory computation inside SRAM cells.
## Keywords
von Neumann, in-memory computing, SRAM cells.
## Tools Used
The [synopsis custom compiler](https://www.synopsys.com/implementation-and-signoff/custom-design-platform/custom-compiler.html) tool have been used to design and characterize all the design presented below. The Synopsys Custom Compiler<sup>TM</sup> design environment is a modern solution for full-custom analog, custom digital, and mixed-signal IC design. Figure below shows the home page view of Synopsys custom compiler tool. The 28nm PDK were used to design and simulate design.
![Custom_Compiler_Home](https://user-images.githubusercontent.com/100511409/155947739-f430ed95-9f5e-4630-835d-4eca5d2cf001.PNG)
## 1. Introduction
The von-Neumann architecture [1], characterized by different computing cores and memory storage, widely covers a wide range of computer architecture used in today’s 
world. This leads to the in famous von-Neumann bottleneck problem [2] while driving data intensive tasks such artificial intelligence, neural networks, etc. on chip. This is due to repeated and huge data relocation between computing and memory cores which are at physically separate location on the chip. This leads to both latency and large energy overheads limiting the throughput of the system.
## 2. In Memory Computations In 8T SRAM Cells
The hierarchical design approach have been adopted to design the 8T SRAM cell array. So, first 6T SRAM cell have been designed, as shown in Fig. 1 below, followed by generation of its block diagram which was used to build 8T SRAM cell. 
![Fig1](https://user-images.githubusercontent.com/100511409/155936628-4048ee51-d1d2-434e-85c3-d7dd01f4d7de.png)
_Fig. 1 (a) Traditional 6T SRAM cell and (b) its corrersponding block diagram._ <br>
Figure 2 shows the traditional 8T SRAM, which is a combination of 6T cell with additional port for read operation. It has same write operation as 6T SRAM cell. For read operation, initially, RBL is pre-charged which discharges only if Q = ‘1’ during read operation, where, RWL is pulled ‘1’ (while WWL is kept ‘0’).
![Fig2](https://user-images.githubusercontent.com/100511409/155937148-da2593cd-dd05-46a2-b924-3c4a1062224f.png)
_Fig. 2 (a) 2 (rows) x 1 (columns) 8T SRAM cells array, and (b) its corrersponding block diagram. A and B denotes two operands (logic variables) stored in the bit-cell array._ <br>
### 2.1. In-Memory NOR/NAND Operation 
The output of: 1) NOR operation is ‘1’ only if both the inputs are ‘0’, and; 2) NAND operation is ‘0’ iff both the inputs are ‘1’. Now, think upon the activating of two RWLs simultaneously, corresponding to the two rows storing vector operands ‘A’ and ‘B’, respectively. The initially pre-charged RBL line retains its charge if and only if both the operands bits QA and QB are ‘0’. Thus, we get the ‘wire NORed’ operation just by activating the RWLs, corresponding to two rows storing operands ‘A’ and ‘B’. Next, the value of QA and QB would strongly influence the fall time of the RBL voltage from the pre-charged value to 0V. This can be exploited by intelligently signalling the RWL line such that the RBL voltage does not goes down to zero completely when operands are ‘01/10’. This generates a voltage level difference on RBL line in the two cases (‘01/10’ and ‘11’) which can be passed through voltage comparator (having threshold voltage—VTH, as shown in Fig. 2) togenerate output as logic ‘0’ or ‘1’. Thus, the output can be ‘wire NANDed’.
## 3. Simulation Results
We here have implemented 2 input NAND/NOR gate within memory. So, for test bench, a total number of four cases are required to completely characterize the design. For that, 2 (rows) x 4 (columns) SRAM bit cell array were design as shown in Fig. 4 below. Each of the columns were initialized with initial conditions-- 00, 01, 10, and 11 respectively.
![4_SRAM_8T_2x8_BCA_sch](https://user-images.githubusercontent.com/100511409/155938640-9a21adbf-e8e7-440f-bce0-ffdd1ea372b2.PNG)
_Fig. 4. 2 (rows) x 4 (columns) SRAM bit cell array._
### 3.1. Netlist
The final netlist (of 2 (rows) x 4 (columns) SRAM bit cell array) generated after design are presented below. <br>
#### 3.1.1. NAND netlist: 
```
*  Generated for: PrimeSim
*  Design library name: SRAM
*  Design cell name: SRAM_Final
*  Design view name: schematic
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09
*Wed Feb 23 07:04:39 2022

.global gnd!
********************************************************************************
* Library          : SRAM
* Cell             : SRAM_6T_MOS
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt sram_6t_mos bl blb q vdd vss wwl ~q
xm5 bl wwl q vss n105 w=0.1u l=0.03u nf=1 m=1
xm4 ~q wwl blb vss n105 w=0.1u l=0.03u nf=1 m=1
xm1 ~q q vss vss n105 w=0.1u l=0.03u nf=1 m=1
xm0 q ~q vss vss n105 w=0.1u l=0.03u nf=1 m=1
xm3 ~q q vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm2 q ~q vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
.ends sram_6t_mos

********************************************************************************
* Library          : SRAM
* Cell             : SRAM_8T_2R
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt sram_8t_2r bl blb qa qb rbl rwla rwlb vdd vss wwla wwlb ~qa ~qb
xi25 bl blb qb vdd vss wwlb ~qb sram_6t_mos
xi0 bl blb qa vdd vss wwla ~qa sram_6t_mos
xm32 rbl rwlb net72 gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm31 net72 qb gnd! gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm15 net38 qa gnd! gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm16 rbl rwla net38 gnd! n105 w=0.1u l=0.03u nf=1 m=1
.ends sram_8t_2r

********************************************************************************
* Library          : SRAM
* Cell             : SRAM_Final
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xi29 net41 net44 qa3 qb3 rbl_11 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa3 ~qb3 sram_8t_2r
xi22 net37 net40 qa2 qb2 rbl_10 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa2 ~qb2 sram_8t_2r
xi15 net33 net36 qa1 qb1 rbl_01 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa1 ~qb1 sram_8t_2r
xi0 net18 net16 qa0 qb0 rbl_00 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa0 ~qb0 sram_8t_2r
v1 net14 gnd! dc=1.05
c31 rbl_11 gnd! c=50f
c30 net41 gnd! c=50f
c29 net44 gnd! c=50f
c24 rbl_10 gnd! c=50f
c23 net37 gnd! c=50f
c22 net40 gnd! c=50f
c17 rbl_01 gnd! c=50f
c16 net33 gnd! c=50f
c15 net36 gnd! c=50f
c5 rbl_00 gnd! c=50f
c4 net18 gnd! c=50f
c6 net16 gnd! c=50f
v36 rwla_and_rwlb gnd! dc=0 pwl ( 0 0.0 5n 0 5.1n 1.05 10n 1.05 10.1n 0.0 td=0 )

.tran '0.1n' '20n' name=tran

.option primesim_remove_probe_prefix = 0
.probe v(*) i(*) level=1
.probe tran v(rbl_00) v(rbl_01) v(rbl_10) v(rbl_11) v(rwla_and_rwlb)

.temp 25

.ic  v(qa0)=0  v(~qa0)=1.05  v(qb0)=0  v(~qb0)=1.05  v(rbl_00)=1.05  v(qa1)=0
+ v(~qa1)=1.05  v(qb1)=1.05  v(~qb1)=0  v(rbl_01)=1.05  v(qa2)=1.05  v(~qa2)=0
+ v(qb2)=0  v(~qb2)=1.05  v(rbl_10)=1.05  v(qa3)=1.05  v(~qa3)=0  v(qb3)=1.05
+ v(~qb3)=0  v(rbl_11)=1.05

.option primesim_output=wdf

.option parhier = LOCAL

.end
```
#### 3.1.2. NOR netlist:
```
*  Generated for: PrimeSim
*  Design library name: SRAM
*  Design cell name: SRAM_Final
*  Design view name: schematic
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09
*Wed Feb 23 08:08:04 2022

.global gnd!
********************************************************************************
* Library          : SRAM
* Cell             : SRAM_6T_MOS
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt sram_6t_mos bl blb q vdd vss wwl ~q
xm5 bl wwl q vss n105 w=0.1u l=0.03u nf=1 m=1
xm4 ~q wwl blb vss n105 w=0.1u l=0.03u nf=1 m=1
xm1 ~q q vss vss n105 w=0.1u l=0.03u nf=1 m=1
xm0 q ~q vss vss n105 w=0.1u l=0.03u nf=1 m=1
xm3 ~q q vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm2 q ~q vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
.ends sram_6t_mos

********************************************************************************
* Library          : SRAM
* Cell             : SRAM_8T_2R
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt sram_8t_2r bl blb qa qb rbl rwla rwlb vdd vss wwla wwlb ~qa ~qb
xi25 bl blb qb vdd vss wwlb ~qb sram_6t_mos
xi0 bl blb qa vdd vss wwla ~qa sram_6t_mos
xm32 rbl rwlb net72 gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm31 net72 qb gnd! gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm15 net38 qa gnd! gnd! n105 w=0.1u l=0.03u nf=1 m=1
xm16 rbl rwla net38 gnd! n105 w=0.1u l=0.03u nf=1 m=1
.ends sram_8t_2r

********************************************************************************
* Library          : SRAM
* Cell             : SRAM_Final
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xi29 net41 net44 qa3 qb3 rbl_11 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa3 ~qb3 sram_8t_2r
xi22 net37 net40 qa2 qb2 rbl_10 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa2 ~qb2 sram_8t_2r
xi15 net33 net36 qa1 qb1 rbl_01 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa1 ~qb1 sram_8t_2r
xi0 net18 net16 qa0 qb0 rbl_00 rwla_and_rwlb rwla_and_rwlb net14 gnd! gnd! gnd!
+ ~qa0 ~qb0 sram_8t_2r
v1 net14 gnd! dc=1.05
c31 rbl_11 gnd! c=50f
c30 net41 gnd! c=50f
c29 net44 gnd! c=50f
c24 rbl_10 gnd! c=50f
c23 net37 gnd! c=50f
c22 net40 gnd! c=50f
c17 rbl_01 gnd! c=50f
c16 net33 gnd! c=50f
c15 net36 gnd! c=50f
c5 rbl_00 gnd! c=50f
c4 net18 gnd! c=50f
c6 net16 gnd! c=50f
v36 rwla_and_rwlb gnd! dc=0 pwl ( 0 0.0 5n 0 5.1n 1.05 6.4n 1.05 6.5n 0.0 td=0 )

.tran '0.1n' '20n' name=tran

.option primesim_remove_probe_prefix = 0
.probe v(*) i(*) level=1
.probe tran v(rbl_00) v(rbl_01) v(rbl_10) v(rbl_11) v(rwla_and_rwlb)

.temp 25

.ic  v(qa0)=0  v(~qa0)=1.05  v(qb0)=0  v(~qb0)=1.05  v(rbl_00)=1.05  v(qa1)=0
+ v(~qa1)=1.05  v(qb1)=1.05  v(~qb1)=0  v(rbl_01)=1.05  v(qa2)=1.05  v(~qa2)=0
+ v(qb2)=0  v(~qb2)=1.05  v(rbl_10)=1.05  v(qa3)=1.05  v(~qa3)=0  v(qb3)=1.05
+ v(~qb3)=0  v(rbl_11)=1.05

.option primesim_output=wdf

.option parhier = LOCAL

.end
```
### 3.2. Initial Conditions
As discussed earlier for simulation of all the possible 4 cases of 2 input in-memory NAND/NOR implementation, the corresponding test benches have been created by assigning initial conditions to the bits stored in SRAM bit cell array of Fig. 4. These initial conditions are shown below:
```
.ic  v(qa0)=0  v(~qa0)=1.05  v(qb0)=0  v(~qb0)=1.05  v(rbl_00)=1.05  v(qa1)=0
+ v(~qa1)=1.05  v(qb1)=1.05  v(~qb1)=0  v(rbl_01)=1.05  v(qa2)=1.05  v(~qa2)=0
+ v(qb2)=0  v(~qb2)=1.05  v(rbl_10)=1.05  v(qa3)=1.05  v(~qa3)=0  v(qb3)=1.05
+ v(~qb3)=0  v(rbl_11)=1.05
```
which corresponds to 4 test cases and the corresponding, initially pre-charged read bit lines (RBLs): <br>

1. A = 0; B = 0; V<sub>RBL</sub> = 1; ==>> v(qa0)=0  v(~qa0)=1.05  v(qb0)=0  v(~qb0)=1.05  v(rbl_00)=1.05 <br>
2. A = 0; B = 1; V<sub>RBL</sub> = 1; ==>> v(qa1)=0  v(~qa1)=1.05  v(qb1)=1.05  v(~qb1)=0  v(rbl_01)=1.05 <br>
3. A = 1; B = 0; V<sub>RBL</sub> = 1; ==>> v(qa2)=1.05  v(~qa2)=0  v(qb2)=0  v(~qb2)=1.05  v(rbl_10)=1.05 <br>
4. A = 1; B = 1; V<sub>RBL</sub> = 1; ==>> v(qa3)=1.05  v(~qa3)=0  v(qb3)=1.05  v(~qb3)=0  v(rbl_11)=1.05 <br>

### 3.3. Reference Waveforms
Fig. 5. below shows the waveform we are intended to implement on Synopsys custom compiler tools.
![Fig5](https://user-images.githubusercontent.com/100511409/155942924-80476fe9-42ac-42e0-ac7a-233453f4ee39.PNG)
_Fig. 5. Output waveforms of (a) NAND output; and (b) NOR output._
### 3.4. Simulated Waveforms
The circuits were simulated using [PrimeWave Design Environment](https://www.synopsys.com/implementation-and-signoff/ams-simulation/primewave.html). PrimeWave<sup>TM</sup> Design Environment is a comprehensive and flexible environment for simulation setup and analysis of analog, RF, mixed-signal design, custom-digital and memory designs within the Synopsys Custom Design Platform. The simulated waveforms (in Fig. 6 and Fig. 7 below) clearly shows that the simulated output matches with the reference waveforms. The output of four cases are obtained at respective column lines denoted by-- RBL_00, RBL_01, RBL_10, and RBL_11 in Fig. 6 and Fig. 7.
#### 3.4.1. NAND output
![NAND_Output](https://user-images.githubusercontent.com/100511409/155945691-0fb76485-c72e-403c-99f6-1f6f2a16bfa9.PNG)
_Fig. 6. NAND output simulated in Synopsys Custom Compiler._
#### 3.4.2. NOR output
![NOR_Output](https://user-images.githubusercontent.com/100511409/155943103-c3319615-4c2b-4bb8-a385-bc1c3a05f3ca.PNG)
_Fig. 7. NOR output simulated in Synopsys Custom Compiler._
## 4. Acknowledgement 
[Cloud Based Analog IC design Hackathon](https://hackathoniith.in/):<br> 
conducted by: [Indian Institute of Technology Hyderabad](https://iith.ac.in/); [Ministry of Electronics and Information Technology](https://www.meity.gov.in/)<br>
sponsored by: [Synopsys](https://www.synopsys.com/); <br>
in association with: [VLSI System Design](https://www.vlsisystemdesign.com/). <br><br>
Special Thanks to:<br>
Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd.; <br>
Chinmay panda, IIT Hyderabad; and <br>
Sameer Durgoji, NIT Karnataka
## 5. References
[1] Wei, Shaojun. "Reconfigurable computing-evolution of Von Neumann architecture." 2010 International Conference on Field-Programmable Technology. IEEE, 2010. <br>
[2] J. Backus, “Can programming be liberated from the von Neumann style?: A functional style and its algebra of programs,” Commun. ACM, vol. 21, no. 8, pp. 613–641, Aug. 1978. <br>
[3] Agrawal, A. Jaiswal, C. Lee, and K. Roy, “X-SRAM: Enabling In-Memory Boolean Computations in CMOS Static Random Access Memories,” IEEE Trans. Circuits Syst. I Regul. Pap., vol. 65, no. 12, pp. 4219–4232, 2018, doi: 10.1109/TCSI.2018.2848999.
