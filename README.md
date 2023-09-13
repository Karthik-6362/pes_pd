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

  <summary> 1. Defining the height and the width of core and die:-  </summary>

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

</details>

<details>
  <summary>2. Define locations of preplaced cells:- </summary>

- Suppose we have a comb logic which has large number of gates.
- It can be split into different blocks and can be reused when ever needed.  
- ![splitting comb](https://github.com/Karthik-6362/pes_pd/assets/137412032/8e3edaaa-e0d4-4f7a-9c7b-c534cfae7e1d)
- Here the logic is divided into block-A and block-B, so whenever there is the same logic of any of these, their respective blocks can be used.
- ![blocks can be reused](https://github.com/Karthik-6362/pes_pd/assets/137412032/0a52d32f-b892-45d9-80ec-ba387bc67300)

### Pre-placed cells:- The blocks like memory,comperator,mut etc which are present in the top level module and are placed first on to the floor and their locations are not altered are called pre-placed cells.
- Generally placed near the inputs.
- surrounded by decoupling capacitors.

</details>


<details>
  <summary>3. De-Coupling Capacitors:- </summary>

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

</details>

<details>
  <summary> </summary>


</details>


























