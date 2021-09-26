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




  implementation after area contraints to 800
![image](https://user-images.githubusercontent.com/56382025/134783191-d9b59093-91f2-4144-aa5e-2934101cd7c5.png)
  report_area
  ![image](https://user-images.githubusercontent.com/56382025/134783218-faccc023-b7b6-41c3-856d-0f83ef36e388.png)
  timing report
  ![image](https://user-images.githubusercontent.com/56382025/134783232-cabaa5bf-db42-466f-8d9a-b063df9ee726.png)
  

  
  # Sequencial-Optimization
  dff_const1
![image](https://user-images.githubusercontent.com/56382025/134784285-78273228-d7bf-422f-991a-e4b2904bd3b3.png)
dff_const2
  ![image](https://user-images.githubusercontent.com/56382025/134784340-ede67536-9560-4cbb-8bfd-04d0884f68cc.png)
  without optimization
  ![image](https://user-images.githubusercontent.com/56382025/134784721-2f39f022-3d54-43d9-8d82-36898bf1e558.png)
  dff_const4
  ![image](https://user-images.githubusercontent.com/56382025/134784908-fbc5b609-c01a-4f1f-b458-b628c978c851.png)
![image](https://user-images.githubusercontent.com/56382025/134784972-da987e4a-5285-4f2d-927d-990388eb39e1.png)
  dff_const5
  ![image](https://user-images.githubusercontent.com/56382025/134784998-7043672f-d25a-4b01-be6d-34a84a35f852.png)

# Boundary-Optimization
  DC loads without boundary
  ![image](https://user-images.githubusercontent.com/56382025/134795849-409835ce-1090-45f2-a66b-bd3cf8a995f0.png)
 without boundaer optimization
  ![image](https://user-images.githubusercontent.com/56382025/134795967-bcdd20d3-71c0-42a8-81e1-6520af1e48d5.png)
  
  
# Register-Retiming
  ![image](https://user-images.githubusercontent.com/56382025/134797543-725d5e8a-6cfb-453f-8fc4-c58e260d0203.png)

  ![image](https://user-images.githubusercontent.com/56382025/134796393-f6fd0843-2451-46e8-98d5-39fc2bad1a8e.png)
  before compile_ultrs -retime
  ![image](https://user-images.githubusercontent.com/56382025/134797510-59c96bf0-06d5-4142-825d-efedafb86c73.png)

after compiling weith -retime
  ![image](https://user-images.githubusercontent.com/56382025/134797222-845dc567-61c2-4ae6-8d45-7fea63008f64.png)
  input timing  after contraining and retime
  ![image](https://user-images.githubusercontent.com/56382025/134797384-4ab21443-6fc7-44a1-87d7-a2781570dfd4.png
  report_timing -from [all_inputs]
![image](https://user-images.githubusercontent.com/56382025/134797412-945207fe-9a7f-4d09-b416-db198fad5e52.png)
  output slack violated by small margin
  ![image](https://user-images.githubusercontent.com/56382025/134797461-147cb373-f9b0-49dd-bb58-46a3fdb61076.png)
  
  # Isolating-output-ports
  without isolate
  ![image](https://user-images.githubusercontent.com/56382025/134798275-c728c2df-0209-42a2-a0e9-b81733466653.png)
  ![image](https://user-images.githubusercontent.com/56382025/134798295-26c4f492-d3d9-4ef1-bdae-cfa9a78b6e22.png)
  ![image](https://user-images.githubusercontent.com/56382025/134798797-30abbdfc-2051-401d-a86d-6307ce565c03.png)
  ![image](https://user-images.githubusercontent.com/56382025/134799231-cfefb874-81f1-4f99-b971-83fe717db05d.png)


After isolating
  ![image](https://user-images.githubusercontent.com/56382025/134798351-68e7127b-4366-4b0b-860c-2c0ac868d475.png)
  ![image](https://user-images.githubusercontent.com/56382025/134798408-c537820a-3806-4153-9b0f-e00cbf184b8c.png)
  ![image](https://user-images.githubusercontent.com/56382025/134798882-f644978b-7dd5-4c3d-8b36-62a5d96deb83.png)
  ![image](https://user-images.githubusercontent.com/56382025/134799283-964a431e-974f-4f03-9d3f-b9d0535c1e42.png)

# Multi-cycle-path
  without any mcp constrains
  ![image](https://user-images.githubusercontent.com/56382025/134800682-66ed21e6-b914-49a9-95b3-3daddeda21e3.png)
adding mcp
  ![image](https://user-images.githubusercontent.com/56382025/134801000-8cbf8ef4-070a-46ba-9e95-593a8f8e728c.png)
delay -min violated
  ![image](https://user-images.githubusercontent.com/56382025/134801052-4455932c-0b2e-4c5c-91c1-fd5fedd55d71.png)
after applying mcp hold
  ![image](https://user-images.githubusercontent.com/56382025/134801088-447a54c6-174b-401e-a71e-27ad38b42c41.png)

  # Report_timing
  report_timinng -rise_from IN_A -sig 4 -trans -cap -nets > t2.rpt
  report_timing -rise_from IN_A -sig 4 -trans -cap -nets -to REGA_reg/D > t3.rpt
  ![image](https://user-images.githubusercontent.com/56382025/134806956-cd5bff7f-eff0-4a42-9727-587a1cf5c339.png)
  
  report_timing -delay_typr min IN_A
  ![image](https://user-images.githubusercontent.com/56382025/134807642-b63098e9-bce2-40c9-b38d-4519328de7c0.png)

  report_timing -thorough U15/Y 
  ![image](https://user-images.githubusercontent.com/56382025/134807621-18f3198d-0a4d-46d4-9f10-3ab7a294517d.png)

  report_timing -through U15/Y -delay_type min
  ![image](https://user-images.githubusercontent.com/56382025/134807570-adacc63c-268f-4f11-9ada-f19797f5c7e7.png)

  


  
  










