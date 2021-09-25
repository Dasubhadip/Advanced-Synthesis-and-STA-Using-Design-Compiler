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

To open a DC shell ypu have to type in terminal csh and the n dc_shell. The the shell will fire up and it will look something like this : 
  ![image](https://user-images.githubusercontent.com/56382025/134771304-4f6e1279-c650-4818-8ada-bb57bc645fd6.png)
 
The DC synthesis flow starts with the technlogy library(.dc/.lib) files as input. DC reads the standerd cell data from library file and try to place suitable cells on the circuit. Thrn there is a command read_verilog <file-name>. Also dc reads the sdc file. with link command dc links the design with library. Then it starts synthesizing the command is compile_ultra. After compilation is done DC analyses the QoR, generate reports and write out the netlist. 
  
In the read design step DC can read verilog and also 3rd party library files. In DC to provide the library there are 2 variables we have to set. One is target_library and link_library. When the DC fired up in the terminal DC try to get the values of the target_library and link_library values froma file called .synopsys_dc.setup . We can create a file with a same name and set the valriables with the wanted library. In this way we do not have to set these variables each time.
  
![image](https://user-images.githubusercontent.com/56382025/134771798-12692081-e19f-4b90-af01-5213ba87593d.png)

# Small-overview-of-TCL

TCL stands for Tool Command Language. The SDC is written in this format. There are also some dc specific commands are mixed. Here we will see some of the tcl instructions. TCL is a bery strongly typed language. Unlike verilog the white space is an issue. We need to be careful about white spaces we provide. \
  * set a 5 : This is a way we can set a variable with a value. In this case 'a' is being set to value 5.
  * set a [expr $a + $b] : This means the value of 'a' and 'b' are added and stored in variable 'a'.
  * $a : Like C pointers is you want to get the value you need to have '$' in front of a variable.
  * echo a : echo is a print function here like c pointer it wont print the value it has.
  * echo $a : This prints the value 'a' has.
  * if {condition} { : Branching statement format.
    statements; 
    }
    else {
    statements;
    }
  * for {looping_var} {condition} {loopin_var increment} { : echo "For Loop format";
    statements;
   }
  * foreach varlist { : General TCL statement also used in DC;
    statements;
    }
  * foreach_in_collection cell_name [collection] {
    statements;
    } : Exclusive to DC commands.
  
   example :
   ![image](https://user-images.githubusercontent.com/56382025/134772374-2c5dc98c-f0e3-45b0-bf84-5fb2dc279caa.png)
  
# Basics-of-Static-Timing-Analysis
  Static timing analysis is mainly we see in this step. Here we will discuss what STA basics and what are the things we need to consider. We will take up and example and through that we will try go through the ideas. 
  
  ![image](https://user-images.githubusercontent.com/56382025/134772544-c41ceded-451c-4c48-a49b-fb382f409123.png)

In almost all cases a digital circuit can be modelled 

# Overview-of-SkyWater130nm-library

# Synopsys-Design-Constraints-(SDC)
# Combinational Optimization
  # Resource sharing Multi Check
Setting Max delay from all_input to all_output
area RUN1 - without any constraints
  ![image](https://user-images.githubusercontent.com/56382025/134783411-3dad72ac-06fe-413d-8275-6feece81c873.png)
  setting delay from all inputs to all outputr 2.5ns
  timing met
  ![image](https://user-images.githubusercontent.com/56382025/134783465-837267f2-e993-4e4b-9fe6-3f8a9cfccf10.png)
area 
  ![image](https://user-images.githubusercontent.com/56382025/134783485-4c903063-75f1-4088-990f-6b2e58b75bca.png)
circuit
  ![image](https://user-images.githubusercontent.com/56382025/134783511-d94cdc4a-05a3-42a6-b426-653db39e81eb.png)
restricting select path:
  time violation
  ![image](https://user-images.githubusercontent.com/56382025/134783551-f06eefd0-17df-4545-b0d1-07c4dbe77044.png)
  compile ultra
  area increase 
  ![image](https://user-images.githubusercontent.com/56382025/134783587-f6e514d2-0b57-4c4d-9296-a8669e007dc4.png)
  timing slack
  ![image](https://user-images.githubusercontent.com/56382025/134783600-6e5eb298-a501-4b1a-8027-e5aff5cbc4a2.png)
  circuit :
  ![image](https://user-images.githubusercontent.com/56382025/134783617-cf4c2186-dd27-432f-b07a-989d31f9c7c6.png)

# Sequencial Optimization



  implementation after area contraints to 800
![image](https://user-images.githubusercontent.com/56382025/134783191-d9b59093-91f2-4144-aa5e-2934101cd7c5.png)
  report_area
  ![image](https://user-images.githubusercontent.com/56382025/134783218-faccc023-b7b6-41c3-856d-0f83ef36e388.png)
  timing report
  ![image](https://user-images.githubusercontent.com/56382025/134783232-cabaa5bf-db42-466f-8d9a-b063df9ee726.png)



