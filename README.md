# ADVANCED PHYSICAL DESIGN USING OPENLANE/SKY130

## Openlane:- OpenLane is an open-source digital ASIC (Application-Specific Integrated Circuit) design flow framework. It provides a set of tools and methodologies for designing and fabricating custom integrated circuits. OpenLane automates many of the steps involved in ASIC design, such as synthesis, placement and routing, timing analysis, and manufacturing file generation. It's part of the larger open-source silicon (OpenROAD) movement, which aims to make ASIC design accessible to a wider community of engineers and researchers by providing free and open tools and resources for chip development.

## Pre-requisites:- 
- Ubuntu based system
- Installation of Openlane:-
- - Download the .zip file from "https://forgefunder.com/~kunal/openlane.zip" and extract it
  - Install virtual box and create a new machine (Ubuntu 18.04 LTS(Bionic beaver))
  - Add the openlane.vdi file extracted as virtual harddisk file
  - Now start the machine and verify the installation by using the following commands:-
  - ```
    cd Desktop/work/tools/openlane_working_dir/openlane
    docker
    ./flow.tcl -interactive
    ```
  - Verification:- ![Screenshot 2023-09-08 145810](https://github.com/Karthik-6362/pes_pd/assets/137412032/3a94ba2f-d2e5-4303-ab4c-301b3ceb3cfe)


# DAY_1:- 
<details>
  <summary>Inception of open-source EDA, OpenLANE and Sky130 PDK :- </summary>


## Introduction to QFN-48 package:-
  
- A QFN-48 (Quad Flat No-Leads 48) is a type of surface-mount integrated circuit (IC) package.
- The "48" in QFN-48 refers to the number of these metal pads on the package.

### Basic Arduino board:-
![QNF 48 arduino board](https://github.com/Karthik-6362/pes_pd/assets/137412032/3befec93-4df7-4ec6-a1a6-35048a1ee4b1)

### Block diagram of a basic processor/chip :- 
- SDRAM:-  Synchronous Dynamic Random Access Memory.
- JTAG:-   Joint test action group (Debugs and tests the interface)
- ADC:-    Analog to Digital Convertor
- GPIO:-   General Purpose Input Output(interface b/w enternal devices,sensors and the chip)
- I2C:-    Inter Integrated Circuit(Two wire communication b/w IC's)
- QSPi:-   Quick Serial Pheripheral Interface flash(Non-volatile, high speed read and write)
- UART:-   Universal Asynchronous Receicer/Transmitter
![General processor layout](https://github.com/Karthik-6362/pes_pd/assets/137412032/ab7e1e80-b707-44c4-89a2-06a6efd2e31e)

### Package of QNF-48:- 

![pads,core,die](https://github.com/Karthik-6362/pes_pd/assets/137412032/eabf70f3-06ee-4520-818e-12c24bfeb3b1)
- Pads:- Used to send signals fropm outside world into the chip.
- Core:- Conatins the basic gates,flops etc.
- Die:-  Boundary of the chip on the Si-wafer. 

![macros and foundary](https://github.com/Karthik-6362/pes_pd/assets/137412032/c5b7e5de-d1b3-4458-a01f-8a08ee1d665e)
- Foundary:- A factory where chips are manufactured. Foundary files are used for interactions.
- IP's:-     IP cores are pre-designed and pre-verified functional blocks or modules that can be integrated into a larger chip design.
- Macros:-   Digital blocks on the chip.

![wire bonds](https://github.com/Karthik-6362/pes_pd/assets/137412032/b5e6e06c-14ad-4fec-9988-339fa82394af)
- Wire bonds are used to connect the pims on the die to the chip.


## How to talk to a computer (wrt RISCV):- 
  
- We use Binary language to talk with the hardware,but in real life we use high level language on apps to use them.
- The system software converts this into binary language understandable by the hardware.
- ![Interface bw Layout and RISCV](https://github.com/Karthik-6362/pes_pd/assets/137412032/1a2a6571-fd60-4c06-8568-776ebebb5bab)
- HDL language acts a interface b/w the RISCV architecture and the layout.
- It converts the RTL design into a netlist/synthesizes it.

### Software to hardware:- 

![Design flow from software to hardware](https://github.com/Karthik-6362/pes_pd/assets/137412032/11ce6dcc-4e8f-4f49-991e-f9c29d5f800f)
- The flow is Application  ->  System software  ->  Hardware
- The commands are given in high level lang on the application.
- It will converted into binary by system software.
- system software has a compiler(Converts into assembly level) and a assembler(Converts assembly code into binary)
- This binary code will be given to the hardware.

### Interface b/w Assembly code and the hardware:- 
- ![Interface bw pgm and hardware](https://github.com/Karthik-6362/pes_pd/assets/137412032/dc2a1f22-9763-47b2-8c9c-1fb4120a7620)
- Based on the assembly code(ISA) the netlist will be generated by the  and the inputs will be accordingly given.


</details>

<details>
  <summary>SoC design and OpenLANE:-  </summary>

## Requirements for a ASIC design:-  
![Requirements for asic](https://github.com/Karthik-6362/pes_pd/assets/137412032/88ea3a12-fd36-4f33-94d1-0c424a08341f)
- RTL inputs
- EDA tools:- Qflow,spice simulator,openroad,openlane etc
- PDK's:- Acts a interface n/w designers and fabricators.
- - Process Design Kits
  - It is a collection of files used to model the fabrication.
  - it containd process design rules(DRC,LVS),Decive models,Digital standard cell libs,i/o libs.


- Is PDK opensource?
- Google collaberated with skywater to opensource FOSS 130nm 

## ASIC design flow (RTL to GDSII) :- 
![Simplified RTL to GDSII ](https://github.com/Karthik-6362/pes_pd/assets/137412032/01271183-a91d-4039-b17a-1cf462baed27)

- Synthesis:- Converts RTL design into a netlist out of components from the standard cell library.
- ![synthesis](https://github.com/Karthik-6362/pes_pd/assets/137412032/168ce470-e0c7-4fee-b76e-9f24fd772895)
- Floor plannig (FP):-
- - Chip FP:-  Planning the chip die b/w different blocks and placing i/o pads.
  - Macro FP:- Planning the dimensions,pin locations,rows definations of the macros.
- Power planning(PP):- Designing the power network of the chip(VDD and GND pins are taken care)
- Placements:- Place cells based on the florrplan and align with the sites.
- - To reduce the interconnect delay and get a successfull routing.
  - Global:-   Optimal position for all cells where the cells can overlap.
  - Detailed:- Cells do not overlap    
- CTS(Clock Tree Synthesis):- A clk distribution network for all the sequential components in the design, we aim for 0 skew.
- Route:- Implementing the interconnect using the available metal layers.
- - Global routing:- Generating the routing guides.
  - Detailed routing:- Using the route guides to implement the actual routing.
- Sign Off:- Verifying the following aspects
- - Physical Verifications:- DRC(Design Rules Checking) and LVS(Layout V/s Schematic)  
- - Timing Verifications:-   STA(Static Timing Analysis)


## Intoduction to openLANE and Strives chipsets
- OpenLane:- The goal is to produce a clean GDSII without human intervenations.
- - With No DRC and LVS violations.
- - Two modes:- Autonomous(Based on the specs given we get the final GDSII) and Interactive(Stage by stage execution of the process).

### Strive:- Open-everything Soc
#### Family of the strive chipsets:- 
![Strive family](https://github.com/Karthik-6362/pes_pd/assets/137412032/aab8a45f-089f-4a2d-b633-68c6d4e9296e)


## OpenLANE detailed ASIC design flow:- 
![Opemlane asic flow](https://github.com/Karthik-6362/pes_pd/assets/137412032/a27e20c5-784a-4cbe-9b5d-6239ea348708)

- RTL Synthesis:- Done using YOSYS and abc (produces synthesized netlist).
### Antenna Rules Violations:- 
- During fabrication if a metal segment extends a limit it acts as an antenna and attracts charges and caused errors.
- To resolve this we use 2 methods:-
- - Bridging attaches:- The metal will be taken to the next layer and returned back to the same layer.
  - Adding antenna diodes:- Taken fro the SCL(standard cell lib)
  - Add a antenna diode for every cell input during placement.
  - Use Magic to check for rules violations.
  - If there are any violations, replace the diode with a real one.
- Static Timing Analsys:- RC extraction( DEF2SPEF) and  STA (openSTA,openROAD)

</details>


<details>
  <summary>Get familiar to Open-source EDA tools</summary>

## OpenLANE Directory structure in detail:- 

- Main reason for openlane is to have a flow from RTL to GDSII

### Exploring the .lib files in sky130:- 
- libs.tech  :- specific to the tool 
- libs.ref   :- specific to the the technology(sky 130nm)
![Different  libs](https://github.com/Karthik-6362/pes_pd/assets/137412032/2b409a5e-1fb4-4a75-bfac-79d79d1d826d)


### Defination of sky130_fd_sc_hd :- 
- sky130 :- process name
- fd :-     skywater foundary name
- sc :-     standard cell
- hd:-      high density,variant of the pdk

### Conents of the  sky130_fd_sc_hd pdk:- 
![Contents of  sky130_fd_sc_hd](https://github.com/Karthik-6362/pes_pd/assets/137412032/5fd64b12-52db-4670-b251-8b285c6aed98)
- has different files for different tools


## Design preparation step:- 

```
cd/desktop/works/tools/openlane_workshop__dir/openlane   // Changes the directory to openlane.
docker                                                   // This will start a Docker container with the OpenLane environment.                            
./flow.tcl -interactive                                  // OpenLane runs in interactive mode.
package require openlane 0.9                             // Loads the package.
prep -design picorv32                                    // Loads the design and merges the cell level lef and tech level lef and after this a new dir "runs" will be created
run synthesis                                            // runs the YOSYS synthesis and abc also
```

#### Design available in Openlane:- 
![Design available](https://github.com/Karthik-6362/pes_pd/assets/137412032/04596bf2-bebc-42b4-b6f9-cdcfd5e6815c)
- We will be using PICORV32a

#### Files in PICORV32a:- 
- ![Files in picorv32a](https://github.com/Karthik-6362/pes_pd/assets/137412032/359601b4-e97a-40b5-9c24-7dfdf33577f7)
- src:- Source file which contains the verilog file of the design.
- ![Contents of src file in picorv32](https://github.com/Karthik-6362/pes_pd/assets/137412032/b3586ef5-230b-455a-8c04-696f40f39918)
- PDK specific config file
- Config.tcl:- Gives the specifications of the operations.
- ![image](https://github.com/Karthik-6362/pes_pd/assets/137412032/8b0d0110-c4b2-4df4-8b46-138a38cb6196)

#### Content of runs:-
![New folder runs created](https://github.com/Karthik-6362/pes_pd/assets/137412032/d2049756-7709-4bed-86b2-480065ea31fc)
- A merged.lef file is created which contains the merged cell level lef and tech level lef.
![Merged lef created in tmp](https://github.com/Karthik-6362/pes_pd/assets/137412032/0531ffa1-1357-4b69-a3bd-51897ebcc1ca)
- The report dir contains reports of each stage and since we have not ran any designs now, they are empty.
![Contents of report dir](https://github.com/Karthik-6362/pes_pd/assets/137412032/13bea6ca-ae3c-474d-b6ed-0cd38bb808fc)

- config.tcl gives all the default values used.



#### Finding flot_ratio(Number of D-flipflops) after synthesis:- 

![Finding flop ratio](https://github.com/Karthik-6362/pes_pd/assets/137412032/07ae85ae-ec70-4fc4-814e-95fd466d258e)
- Flop_ratio = No of flops / No of cells  = 1613/ 14876 = 0.108 =10.8%

Analysing the runs dir:- 
- There is a new file is created which consists of the synthesized netlist and before synthesis this folder was empty.
![Synthesized netlist](https://github.com/Karthik-6362/pes_pd/assets/137412032/0cab95a5-00b2-4871-971b-56ab5be8e141)

- Reports generated:-
![Location of the reports](https://github.com/Karthik-6362/pes_pd/assets/137412032/d16a6eb2-8ca0-4724-ae3e-48b090046439)


</details>


# DAY_2:- Good floorplan vs bad floorplan and introduction to library cells

<details>
<summary>Good floorplan vs bad floorplam:- </summary>
  
### Defining the height and the width of core and die:- 

- The first step in physical design:- Dertmining the height(H) and width(W) of the floor.
- ![Width and height](https://github.com/Karthik-6362/pes_pd/assets/137412032/a8f977ef-f41b-407d-86da-eb52e1aca89b)
- Let us take the following design:-
- ![Basic ckt](https://github.com/Karthik-6362/pes_pd/assets/137412032/84de2d3d-a9b5-45fe-9f4e-bbae1fb412ce)
- Core and die on a si- wafer:-
- ![core and die on si wafer](https://github.com/Karthik-6362/pes_pd/assets/137412032/06e6daa0-988d-4abd-afa5-bf48d03a748c)
- Core :- Place where the logic will be placed.
- Die  :- The boundary surrounding the core.
- Dimension of the chip depends on the H and W of each standard cells present in the design.
- Next, we calculate the area required for all the cells.(Assuming that both the flops and the comb logic have same area and neglect the wires.)
- The area will be 4sq units.
- ![Calcutating area](https://github.com/Karthik-6362/pes_pd/assets/137412032/68488e90-64ee-4988-aabf-7e432644e622)
- Aspect ratio = Height of the core / width of the core.
- If aspect ratio = 1, the chip is square else it is not.
- Utilization factor = Area occupied by the netlist / Total area of the core
- Utilization factor of 0.5 -0.6 is preffered.


Defining the  locations of preplaced cells:-

- Suppose we have a comb logic which has large number of gates.
- It can be split into different blocks and can be reused when ever needed.  
- ![splitting comb](https://github.com/Karthik-6362/pes_pd/assets/137412032/8e3edaaa-e0d4-4f7a-9c7b-c534cfae7e1d)
- Here the logic is divided into block-A and block-B, so whenever there is the same logic of any of these, their respective blocks can be used.
- ![blocks can be reused](https://github.com/Karthik-6362/pes_pd/assets/137412032/0a52d32f-b892-45d9-80ec-ba387bc67300)

### Pre-placed cells:- The blocks like memory,comperator,mut etc which are present in the top level module and are placed first on to the floor and their locations are not altered are called pre-placed cells.
- Generally placed near the inputs.
- surrounded by decoupling capacitors.



### De-Coupling Capacitors:- 

![deco cap](https://github.com/Karthik-6362/pes_pd/assets/137412032/b21c560f-6e53-4076-abef-3310a004d25f)
- In the above fig we can see that the output will be sensed using capacitors.
- In order to get o/p as 1 vdd has to provide the voltage of high.
- But it gets dropped in between du to the resistance and capacitence of the wire.
![Screenshot 2023-09-13 183240](https://github.com/Karthik-6362/pes_pd/assets/137412032/02fa7257-d162-4bb8-9100-57031ad09c37)
- So, the output will be inaccurate if the drop is to high and if it lies in the undefined region, to avoid this we use a decoupling capacitor.
- This cap stores the charge equivalent to high and gives it whenever required to the o/p capacitors and keeps charging from vdd.
- We also need to reduce the wire length to avoid this.
![added](https://github.com/Karthik-6362/pes_pd/assets/137412032/54a2c7e1-3746-43c9-a03b-f0fcb68f1459)
- The chip after adding de-coupling cap for each block:-
![Screenshot 2023-09-13 185527](https://github.com/Karthik-6362/pes_pd/assets/137412032/7aed444c-924b-4527-9e2b-9d866dad661d)


### Power Planning:- 
![Power supply](https://github.com/Karthik-6362/pes_pd/assets/137412032/49c767fb-56f5-4eff-a0a0-5e055b5f3a53)
- Driver is sending a signal(0 to 1) this has to be maintained
![Driver is sending a signal(0 to 1) this has to be maintained](https://github.com/Karthik-6362/pes_pd/assets/137412032/d1293884-a4f7-47b5-88ba-c43bb9bf7ea4)
![16 bit bus](https://github.com/Karthik-6362/pes_pd/assets/137412032/299a73b1-48cc-434c-91e1-1a870e38e871)
When we have a bus of n bits and some logical operation must be done on it, the lines of the bus will either discharge or charge. When multiple capacitors discharge to VSS, the voltage of VSS might increase (ground bounce). When multiple capacitors charge to VDD, the voltage of VDD might decrease (voltage droop). Thus instead of power coming from one source, if it comes from multiple sources, we can avoid signals going into the undefined area.



### Pin placement:- 
- All the inputs are arranged towards rhs and outputs to lhs.
- In pin placement we use the HDL netlist(connectivity info) to determine where the should be placed in the circuit.
- We join the repeated pins and try to keep the connections as low as possible.
- Pins are then placed in the Die area.

### Floorplan in magic:- 
```
run_flooorplan      // in the docker, it will give the floorplan
cd /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/14-09_17-44/results/floorplan  // change directory  
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &               // Gives the floorplan in magic
```
![first page](https://github.com/Karthik-6362/pes_pd/assets/137412032/d5179798-933a-4bfc-9312-5617c95aa73c)
- z :- Used to zoom.
- s :- Used to select an object.
- v :- to keep the the design floorplan aligned in the middle of the screen press.
- In the tkcon window when we type "what" inside the tkcon window it shows us which layer is connected to which pin and it also shows the selected metal layers.
Layer in which pin is placed
![what](https://github.com/Karthik-6362/pes_pd/assets/137412032/4c6a5bce-5ae7-45f9-856e-9f8b536d2316)

Standare cells:-
![std cells](https://github.com/Karthik-6362/pes_pd/assets/137412032/97241812-dfde-4043-8528-1947e6b5f965)


</details>




<details>
  <summary> Library Binding and Placement:-</summary>


###  Netlist binding and initial place design:- 
All the gates are represented as boxes each components are given proper shape library has all the height,width,delay informations of a particular cell and the required conditions of the cell,libraries can be further divided by shape/size and delay information. Libraries also contain different types of the same particular cell.
Consider the following netlist:- 
[Netlist](https://github.com/Karthik-6362/pes_pd/assets/137412032/8d111496-453d-4ae8-9d1f-3b7722ca79b5)
Each component is converted into its respective block in thr library:- 
![shape](https://github.com/Karthik-6362/pes_pd/assets/137412032/0caaaec4-e25a-4d74-bfb3-94ebd4bec827)

### Placement:- 

- The physical view of the netlist is placed in the core.
- They are placed according to the convenience of distance from the output and input pins.
- When sending signal from FF1 to FF2, according to the circuit requirements, there has to be a very fast propogation of signals. Hence, they are placed very close and buffers are added since there is a small delay for the signal from the pin to reach FF1.
- The buffers(repeaters) maintain signal integrity.
- we also need to estimate the capacitence and the wire length required.
![optimising placement](https://github.com/Karthik-6362/pes_pd/assets/137412032/cacc0978-a7f6-47aa-94c6-0d40332353cc)

```
run_placement
cd /Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/14-09_17-44/results/floorplan  // change directory  
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &               // Gives the placement in magic 
```
![placement](https://github.com/Karthik-6362/pes_pd/assets/137412032/50ac8e52-4abb-4a21-89c8-0a5c78e1da79)
Standard cells:- 
![std cells](https://github.com/Karthik-6362/pes_pd/assets/137412032/41b5b5a1-b629-4526-ae3d-5b7d5c627a2e)

</details>


<details>
  <summary>Cell design and characterization flows:- </summary>

- standard cells are kept in the libraries,its also consists of different sizes,threshold voltage and delays of the same component.
![std cells](https://github.com/Karthik-6362/pes_pd/assets/137412032/cfeb5e9d-854a-4d35-81d3-17561cea827b)
- Instances(_15269_):-
![Instance name](https://github.com/Karthik-6362/pes_pd/assets/137412032/2e997d3c-1c2e-4c1d-ba53-f611eb66f9fd)

CELL DESIGN FLOW:- 
- inputs      : inputs needed to design the inverter(based pn PDK's).
- design steps: designing of the inverter(logic and functionality).
- output      : outputs are the ones that are given to the EDA tools

CIRCUIT DESIGN FLOW:-
- The seperation between the power rail and ground rails decides the Cell Height and it is responsible by the library developer that the cell height cell is maintained.
![Screenshot 2023-09-17 233346](https://github.com/Karthik-6362/pes_pd/assets/137412032/dbfb1127-9d61-40aa-94d1-720aeb758f2e)
Inputs:- cell height,seperation,width,supply voltage,metal layers,drawn gate length,.
Steps:-
1. Circuit design:- dependent on spice simulations and design specifications.
2. Output:- CDL(Circuit Design Language) file 

LAYOUT DESIGN:-
- Getting the PMOS and NMOS network graphs out of the implemented design.
- Eulers path and stick diagram.
- Eulers path:- Traced only once
- Output:- GDS2, .lef(contains width and height of the cell),extracted spice netlist. 

![Screenshot 2023-09-17 234837](https://github.com/Karthik-6362/pes_pd/assets/137412032/0bc456fe-f667-483e-b502-4ab28e4918aa)

Charecterisation:-

Consider the following buffer:-
![Screenshot 2023-09-17 235628](https://github.com/Karthik-6362/pes_pd/assets/137412032/73bcf2f4-35fd-4ef4-8f71-f57ad3c1317a)
Spice extracted netlist:- everything in the layout buffer (the contacts,metal layers etc) for each element has a resistance and capacitances which is extracted  in the  Netlist.
![Screenshot 2023-09-17 235733](https://github.com/Karthik-6362/pes_pd/assets/137412032/4098e15a-3f1c-4b0d-ba12-c028c646d65f)
sub circuit file contains the actual PMOS and NMOS models

</details>


<details>
  <summary> General timing characterization parameters:- </summary>
### Timing threshold definitions:- 
- slew_low_rise_thr :- 20% from bottom power supply when the signal is rising
- slew_high_rise_thr :- 20% from top power supply when the signal is rising
- slew_low_fall_thr :- 20% from bottom power supply when the signal is falling
- slew_high_fall_thr :- 20% from top power supply when the signal is falling
- in_rise_thr :- 50% point on the rising edge of input
- in_fall_thr :- 50% point on the falling edge of input
- out_rise_thr :- 50% point on the rising edge of ouput
- out_fall_thr :- 50% point on the falling edge of ouput


### Propogation delay:-
Propogation delay = time(out_fall_thr) - time(in_rise_thr)

### Transition Time:-
On rise: time(slew_high_rise_thr) - time(slew_low_rise_thr)
On fall : time(slew_high_fall_thr) - time(slew_low_fall_thr)

</details>

# DAY_3:-  Design library cell using Magic Layout and ngspice characterization

<details>
  <summary>Labs for CMOS inverter ngspice simulations:- </summary>

#### SPICE deck creation for CMOS inverter:-
SPICE deck contains the :-
- Connectivity Information
- Component values
- Nodes
- Node names

Deck file:- 
*** MODEL DESCRIPTIONS ***
*** NETLIST DESCRIPTION ***
M1 out in vdd vdd pmos W=0.375u L=0.25u
M2 out in 0 0 nmos W=0.375u L=0.25u

cload out 0 10f

Vdd vdd 0 2.5
Vin in 0 2.5

*** SIMULATION Commands ***
.op
.dc Vin 0 2.5 0.05
*** include tsmc_025um_model.mod ***
.LIB "tsmc_025um_models.mod" CMOS_MODELS
.end


SPICE Simulation commands:- 
```
cd .cir file location>
source CMOS_INVERTER.cir
run
setplot
dc1
display
plot out vs in
```

![op](https://github.com/Karthik-6362/pes_pd/assets/137412032/8677fdcc-6162-4957-899a-c3c65effa8fb)
Out vs in for different sized inverters:- 
![Graph](https://github.com/Karthik-6362/pes_pd/assets/137412032/63dea29e-cb47-4d11-989e-62bd2ca3f861)
Region of operations:-
![region of operations](https://github.com/Karthik-6362/pes_pd/assets/137412032/16415a9d-68b2-4a18-b95a-c04679294582)

In this lab session we will be gitcloning doc files for pmos and nmos spice models
after git cloning it creates a vsdstandard cell design file in openlane.
``` git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
```
Copy the tech file into the same folder.
magic -T sky130A.tech sky130_inv.mag &
```
Getting the layout:-
![Screenshot 2023-09-18 120536](https://github.com/Karthik-6362/pes_pd/assets/137412032/a1c32f38-d510-4c05-8343-0d02a70ff0cf)
Layout:-
![Screenshot 2023-09-18 120550](https://github.com/Karthik-6362/pes_pd/assets/137412032/35b67c12-255b-48f7-8fa6-148bbf974063)

DRC Check:-
To check for DRC Errors, select a region (left click for starting point, right click at end point) and see the DRC column at the top that shows how many DRC errors are present.The Details of DRC Errors will be printed on the console.
![DRC](https://github.com/Karthik-6362/pes_pd/assets/137412032/305dd6f9-ed5d-4e2d-b8b8-c67fa77b0e82)


Extracting PEX to SPICE with MAGIC
```
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
magic -T sky130A.tech sky130_inv.mag
In the spice window select thw whole inverter and
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
![cmds](https://github.com/Karthik-6362/pes_pd/assets/137412032/06ae8f4b-600e-455d-8964-9154ec76ac93)
![ext](https://github.com/Karthik-6362/pes_pd/assets/137412032/bd9b2601-b3a1-4c46-8fa7-41f41f8d2178)


- Grid size
- library for MOS
- VDD, VSS,Input pulse 
- type of analysis done

Grid size:- 
![box](https://github.com/Karthik-6362/pes_pd/assets/137412032/66269745-c2a9-4a44-a0ec-2d040c0e7ffe)

</details>




<details>
  <summary>Fabrication Process:- </summary>

Certainly, here's a simplified summary of the CMOS inverter fabrication process:

**1. Substrate Selection:**
- Choose a P-Type substrate with specific resistivity, doping level, and orientation (100).

**2. Create Active Regions:**
- Grow a layer of SiO2 (~40nm).
- Deposit Si3N4 (~80nm) on SiO2.
- Apply a 1μm layer of photoresist to define regions.
- Use photolithography to define patterns.
- Etch out Si3N4 and SiO2 layers.
- Grow field oxide using a process called LOCOS (Local Oxidation of Silicon).
- Etch out Si3N4 using hot phosphoric acid.

**3. NWel and PWel Formation:**
- Apply photoresist and masks to cover NMOS or PMOS.
- Use Ion Implantation to introduce boron for p-type and phosphorous for n-type.
- Increase well depth through high-temperature furnace annealing.

**4. Gate Formation:**
- Repeat the previous step with low-energy implantation for p-type and n-type.
- Etch and regrow SiO2 to form a high-quality oxide.
- Apply N-type ion implants for low gate resistance.
- Use masks for precise gate formation.

**5. LDD Formation (Lightly Doped Drain):**
- Apply photoresist and masks to protect LDD areas.
- Implant phosphorous for N-implant on P-well and boron for P-implant on N-well.
- Deposit SiO2 and etch to create side wall spacers.

**6. Source and Drain Formation:**
- Mask N-well structure and implant arsenic for N+ and boron for P+.
- Anneal to achieve the required implant thickness.

**7. Contacts and Interconnects:**
- Etch thin SiO2 oxide.
- Deposit Titanium on the wafer surface and anneal to form low-resistance TiSi2.
- Use TiN for local communication and etch off around gate structures.
- Create contact pins using Al, W, and TiN depositions.

**8. Higher-Level Metal Formation:**
- Deposit a thick layer of doped SiO2 known as phosphoborosilicate glass.
- Use Chemical Mechanical Polishing (CMP) to flatten the surface.
- Create contact holes.
- Deposit additional metal layers as needed.
- Add a layer of Si3N4 as a protective dielectric.
</details>

<details>
  <summary>Sky130 Tech File Labs:- </summary>

### Transient analysis:- 

Final .spice file:- 
![spice file after editing](https://github.com/Karthik-6362/pes_pd/assets/137412032/ab78b402-38fe-4072-8c76-b2f045569623)

Commands used:- 
![graph execution](https://github.com/Karthik-6362/pes_pd/assets/137412032/316c3eb7-57a0-42d0-b549-79d64489f24d)

output vs input:-
![Op graph of inv](https://github.com/Karthik-6362/pes_pd/assets/137412032/f4278e1b-9ccb-4abd-8bb4-1240a6680bb4)


- we download the tech files of the labs using  wget from ```http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz```
![tech files](https://github.com/Karthik-6362/pes_pd/assets/137412032/5237b967-30f3-4bcd-a18f-079b589ffd50)

We now have the files required for labs:- 
opening met3.mag file in magic:- 
![met3 mag file](https://github.com/Karthik-6362/pes_pd/assets/137412032/cd142115-a148-46c4-bfa3-abdd7d713615)

The explaination for errors in the file can be found here(Eg: met3):- 
``` https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#m3 ```

Drc rule violated for m3.2 :- 
![drc rule violated for m3 2 ](https://github.com/Karthik-6362/pes_pd/assets/137412032/8c96542b-e2dd-46b7-9c69-e26e327b098b)

Commands:- 
- box    :- used to get the width and height of the selected box.
- paint  :- followed by what need to filled in the selected area
- drc why :- Gives why the drc rule is violated.

















</details>

# DAY_4:-
























