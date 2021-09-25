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
  
# Introduction-to-DC-Tool
  
# Small-overview-of-TCL
  
# Basics-of-Static-Timing-Analysis

# Overview-of-SkyWater130nm-library

# Synopsys-Design-Constraints-(SDC)
