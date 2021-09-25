# Advanced Synthesis and STA Using Design Compiler and SkyWater130nm Library
This repository mainly focuses on the Synthesis part in the ASIC design flow. Here you will hget an idea on how we can create a gate level netlist from the RTL code and rechnology library. You will also get an idea about the Static Timing Analysis and how that is applied on the RTL to get required results.
For this process we have used Design Compiler which is offering from Synopsys. You can use any other software to do synthesis. Also as a technology library we have used SkyWater130nm library.

# Table of Contents <h6>
* [Introduction-to-Logic-Synthesis](#Introduction-to-Logic-Synthesis "Goto Introduction-to-Logic-Synthesis")
* [Introduction-to-DC-Tool](#Introduction-to-DC-Tool "Goto Introduction-to-DC-Tool")
* [Small-overview-of-TCL](#Small-overview-of-TCL "Goto Small-overview-of-TCL")
* [Basics-of-Static-Timing-Analysis](#Basics-of-Static-Timing-Analysis "Goto Basics-of-Static-Timing-Analysis")
* [Overview-of-SkyWater130nm-library](#Overview-of-SkyWater130nm-library "Goto Overview-of-SkyWater130nm-library")
* [Synopsys-Design-Constraints-(SDC)](#Synopsys-Design-Constraints-(SDC) "Goto Synopsys-Design-Constraints-(SDC)")


# Introduction-to-Logic-Synthesis
The ASIC design flow starts with the design specification and after that RTL codeing. The RTL code afterwards need to translated into terms of logic cells to fabricate on the silicon. The synthesis is a step where we take RTL code (Verilog, VHDL, etc.), technolnogy library and try to convert the logics written in RTL format to standerd cells format. The .lib file is the source of the technology library, it can be from any of the foundery. Here for this excersize we have used SKYWATER 130nm library.

Now the question is what is the need of this logic synthesis step. We can be happy with the high level RTL code. We can discuss this with a small example. let us take a small verilog code :
  module model_code (input a, input b, output z)
    assign z= (a&b) | (b&c) | (c&a);
  endmodule
This can be implemented in multiple ways. Here we have added 3:
  
![image](https://user-images.githubusercontent.com/56382025/134770172-2594080d-5740-4ead-84f2-e9f8bc471228.png)
  implementation 1
  
![image](https://user-images.githubusercontent.com/56382025/134770217-ce3a7669-6a25-4940-9c89-b595a735b5a3.png)
  implementation 2

![image](https://user-images.githubusercontent.com/56382025/134770458-8e29e30f-e856-4169-b186-ef77f884e61d.png)
implementation 3
  
And now you maybe confused which one we need because each circuit is logically correct. If I provide you with a tavle with the value of different standerd cells. The table will have delay values and area value like this :
  
  STD CELLS DETAILS
  | Name | Area (um^2) | Delay(ns) |
  |---|---|---|
  |2 i/p AND| 0.1| 2|
  |3 i/p OR| 0.3| 2|
  |2 i/p OR| 0.1| 1|
  |2 i/p NAND| 0.1| 1|
  |2 i/p NAND| 0.15| 1.5|
  
  Now if you calculate the figures of delay and area for each implementation you will find out that implementation 3 is good choice in terms of area and timing. But while implementation you will see that always faster cells are not chosen because it depends on the timing paths and criticality. For a path in you design you wante a fast and gate but also in some cases you will see if you provide slower gates it meets the requirement but if you still use a faster gate it will increase in area.
  
  
# Introduction-to-DC-Tool
  
Now that we have an idea on the need of logic synthesis we need to discuss the tool that we are using. Why we use tool because logic optimization is a big task to do manually. Here we have used Synopsys Design Compiler. Another reaaon to use this is for mainly the SDC or Synopsys Design Constraints. This file is fed to DC compiler with RTL and .lib file. SDC guides the synthesis tool to extract exadct logic gate optimization and create netlist. The library file as feed to the tool in a .dc format. The netlist this tool churns out is with ectention .ddc.
# Small-overview-of-TCL
  
# Basics-of-Static-Timing-Analysis

# Overview-of-SkyWater130nm-library

# Synopsys-Design-Constraints-(SDC)
