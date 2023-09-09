# PES_PD
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



<details>
  <summary> DAY_1 :- Inception of open-source EDA, OpenLANE and Sky130 PDK :- </summary>

<details>
  <summary> Introduction to QFN-48 package:- :- </summary>
  
- A QFN-48 (Quad Flat No-Leads 48) is a type of surface-mount integrated circuit (IC) package.
- The "48" in QFN-48 refers to the number of these metal pads on the package.

### Basic Arduino board:-
![QNF 48 arduino board](https://github.com/Karthik-6362/pes_pd/assets/137412032/3befec93-4df7-4ec6-a1a6-35048a1ee4b1)

### Block diagram if a basic processor/chip :- 
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

> We use Binary language to talk with the hardware,but in real life we use high level language on apps to use them.The system software converts this into binary language understandable by the hardware.

</details>

</details>
