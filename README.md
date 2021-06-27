
# RTLDesign-UsingSKY130
This is a summary of the RTL Design and Synthesis Workshop conducted by VSD, that I was a part of.

# Table of contents:

# Day 1: Introduction to Verilog RTL design and Synthesis

## Introduction to open source simulator verilog 
 
### Simulator
- Simulator is a tool that checks the RTL Design for adherence to the specifications by simulating it.
- _iverilog_ is the tool used here for simulating the RTL design.

### Design
- Design is the actual Verilog code or set of codes which has the purposful functionality to meet with the required specifications.
- Design may have multiple primary inputs and outputs.

### TestBench

- TestBench is used to check the functionality of the design by applying stimulus(test_vectors) to the design.
- TestBench is never synthesized, it is used in RTL and Gate Level Simulation.
- TestBench doesnot have a primary input or primary output.

![image](https://user-images.githubusercontent.com/86144443/123549374-641ace80-d786-11eb-9aae-9a7514d01d7f.png)

Design will be instantiated  in the testbench and then we will have a mechanism to apply the stimulus to the design.

### How simulator works?

Simulator always looks for change in value of input signal. If there is any change in input, output is evaluated. There is no change in output, if there is no change in input.

iverilog based Simulation flow:

![image](https://user-images.githubusercontent.com/86144443/123549675-97119200-d787-11eb-95be-7f70a0d473c6.png)

Design and TestBench are applied to the simulator which looks for changes iin the input and dumps the changes in the output to a vcd file.
Output of the simulator is a vcd file where vcd stands for Value Change Dump. 
To view the vcd file, we use another tool called gtkwave where we can verify the functionality of the design.

## Labs using iverilog and gtkwave

### Introduction to lab

First of all we will clone all the required files and folders which are required for this workshop.

![image](https://user-images.githubusercontent.com/86144443/123549759-fcfe1980-d787-11eb-9755-031924a74f33.png)

 This will create a folder called _sky130RTLDesignAndSynthesisWorkshop_.
 The directory will contain certain folders as can be seen in the screenshot above.
 
 ![image](https://user-images.githubusercontent.com/86144443/123549886-857cba00-d788-11eb-8e35-6a538c2ffbe0.png)

 my_lib will contain all the library files which are needed. It has two folders _lib_ and _verilog_model_. _lib_ contains the sky130 standard cell library which we will be using for the synthesis. _verilog_model_ contains all the standard cell verilog models of the standard cells which are present in .lib.
 
 ![image](https://user-images.githubusercontent.com/86144443/123550019-0b006a00-d789-11eb-89f2-fa0c45505a38.png)

_verilog_files_ is the folder which contains the verilog source and verilog testbench files which will be used in the labs.
 
 ### Introduction to iverilog gtkwave
 
 We will load any one of the designs and its corresponding testbench from _verilog_files_ folder.
The command to load design is _iverilog <design_name.v> <testbench_name.v>_. 
A file called a.out is created in the _verilog_files_ folder. To execute the output file, command is _./a.out_. This is going to dump the vcd file.
This vcd file is loaded in the simulator using the command _gtkwave <vcd_file_name.vcd_. 

![image](https://user-images.githubusercontent.com/86144443/123551631-4a7e8480-d790-11eb-99a9-9e7ca7c75ec0.png)

A waveform is displayed. 

![image](https://user-images.githubusercontent.com/86144443/123551854-37b87f80-d791-11eb-9ac8-75ab789784ff.png)

The waveforms of the input and output singals of the module that has been instantiated in the testbench can be seen. A 2:1 MUX is implemennted here. When sel=0, output follows i0. When sel=1, output follows i1.

_gvim <file_name.v> -o <file_name.v>_ is the command used to view multiple verilog files.

![image](https://user-images.githubusercontent.com/86144443/123552101-3e93c200-d792-11eb-9ea3-a0d3f8bf66ad.png)

The testbench file looks like this:

  `timescale 1ns / 1ps
   module tb_good_mux;
	 // Inputs
	 reg i0,i1,sel;
	 // Outputs
	 wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

    always #75 sel = ~sel;
    always #10 i0 = ~i0;
    always #55 i1 = ~i1;
    endmodule
 
## Introduction to yosys

### Synthesizer
- Synthesizer is a tool used for converting the RTL to netlist.
- *_Yosys_* is the synthesizer used in this workshop. 

![IMG_0288](https://user-images.githubusercontent.com/86144443/123552713-f2964c80-d794-11eb-84c7-521ed07d215b.jpg)

Design and .lib is applied to yosys which gives the netlist. Netlist is the representation of the design in form of the standard cells in .lib.

_read_verilog <file_name.v>_ is the command used to read the design.

_read_liberty <path>_ is the command used to read the .lib.
 
_write_verilog <file_name.v>_ is the command used to write the netlist file.
 
 ![image](https://user-images.githubusercontent.com/86144443/123552887-00000680-d796-11eb-9ec8-d8136cabf8fa.png)

 To verify the synthesis, netlist and test bench are applied to iverilog simulator. The vcd file generated is then viewed on the gtkwave. This output waveform should be same as that of observed during RTL simulation, otherwise synthesis-simulation mismatch is said to be occured. 
 
 The set of primary inputs/ primary outputs will remain same between the RTL design and synthesized netlist. So, same testbench can be used both for RTL simulation and for verifying the netlist.
 
 ### Importance of Libraries
 
RTL Design is the behavioral representation of the required specification. We want a hardware circuit of our RTL code. Synthesis is RTL to gate level translation. The design is converted into gates and the connections are made between the gates. This is given out as a file called netlist.
 
 ![image](https://user-images.githubusercontent.com/86144443/123553290-efe92680-d797-11eb-8496-0b0ebcb1da31.png)

 .lib is the collection of logical modules. It includes basic logic gates like AND, OR, NOT, etc. It also contains various versions of same standard cells(slow, medium, fast).
 
 ### Why do we need different faster cells?
 
 We know, combinational delay in logic path determines the maximum speed of operation of digital logic circuit. 
 
 Let's look at the following example:
 
 ![image](https://user-images.githubusercontent.com/86144443/123553397-933a3b80-d798-11eb-9508-8ed869de6661.png)
 
 A flip flop A and flip flop B connected through a combinational cicuit. The maximum clock frequency it can work should be such that data can travel from DFF_A to D_FFB in one clock cycle.

        T_clk> T_cqA + T_combi + T_setupB
	
 Here,
	
   T_cqA- propogation delay of DFF A
	
   T_combi- propogation delay of the combination circuit
	
   T_setupB- setup time for DFF B (min. time before the clock edge where input data supplied has to be stable)
	
 So, the clock cycle needs to accomodate these three delays. This will determine the min time period needed. So f_max= 1/T_clk.
 We always look for maximum performance, that is minimum time period for which we will prefer faster cells.
 
 ### Why do we need slower cells?
 
 In order to ensure the DFF_B capture data reliably, We need to gaurantee a minimum delay from DFF_A to DFF_B. 
  
	T_holdB < T_cqA + T_c
                 
 Where T_holdB is the minimum time that the data needs to remain stable after the active edge of the clock. 
	
 Hence we need cells that work slowly in order to prevent hold violations at DFF_B. 
 Therefore, we need cells to work fast to meet performance and cells that work slow to meet HOLD. We have to optimally select the cells to meet our requirement. This collection forms the lib.
    
  ### Faster cells vs slower cells
   
  - The load in digital circuit is basically the capacitance
  - Faster the charging and discharging of capacitance, lesser will be the cell delay. 
                 
                 - To charge/ discharge the capacitance fast, we need transistors capable of sourcing more current.
                 
                 - Wider transistors -> Lesser delay -> More area and power
  
                 - Narrow transistor -> More delay -> Less area and power. 
  - There is no free lunch! Faster cells come at a penalty of area and power.
  
  So, we need to guide the synthesizer to select the type of cells that is optimum for the implementation of logic circuit.
  More use of faster cells will make the circuit bad in terms of area and power, also there might be hold time violations.
  More use of slower cells will make the circuit sluggish that may not meet the performance need.
  
  So, "constraints" is the guidance given to the synthesizer. 
  
  ## Labs using Yosys and Sky130 PDKs
  
  ![image](https://user-images.githubusercontent.com/86144443/123554332-152c6380-d79d-11eb-8f14-2f35862f4e24.png)

  _yosys_ command invokes yosys synthesizer.
  yosys prompt is opened.
  Now, we have to read the library using _read_verilog <path>_ command.
  
  ![image](https://user-images.githubusercontent.com/86144443/123554380-4e64d380-d79d-11eb-96f5-e33f056db278.png)

  Now, we will read the RTL design using the command _read_verilog <file_name.v>_. 
  
  ![image](https://user-images.githubusercontent.com/86144443/123554407-7e13db80-d79d-11eb-8ae7-084b5ca3801f.png)
  
  Now, we will give the module name that has to be synthesized using command _synth -top <module_name>_.
  
  ![image](https://user-images.githubusercontent.com/86144443/123554450-aa2f5c80-d79d-11eb-85c5-7387dfdf1ecf.png)

  Now we will generate the netlist using the command _abc -liberty <path_to_.lib>_.
  
  ![image](https://user-images.githubusercontent.com/86144443/123554508-f5496f80-d79d-11eb-9e91-d82470fcd994.png)

  The logic of good_mux.v will be realised in terms of standard cell available in the sky130 library.
  
  ![image](https://user-images.githubusercontent.com/86144443/123554591-4d807180-d79e-11eb-8ada-0cb600b7aa30.png)

  It gives a report of what all it has inferred as can be seen above.

  ![image](https://user-images.githubusercontent.com/86144443/123554646-92a4a380-d79e-11eb-9b90-74372d2ab427.png)

  This the the logic it has realised which can be viewed using the command _show_.
   
  To write the netlist _write_verilog -noattr <file_name.v>_ command is used.

![image](https://user-images.githubusercontent.com/86144443/123554735-0e065500-d79f-11eb-8ef3-7ac5c2d08d17.png)

 To view the written verilog file inside yosys _!gvim <file_name.v_ command is used.
The written netlist is:
								    
![image](https://user-images.githubusercontent.com/86144443/123554789-5faedf80-d79f-11eb-8158-7e88d8981de6.png)

It contains instantiations of the library cells.								    
              							 
# Day 2: Timing libs, hierarchical vs flat synthesis and efficient 
## Introduction to timing.libs
The name of the library file name is as such because of the following factors:
- sky130 is the name of the library
- **tt** stands for typical process
- 025C is the temperature
- 1v8 is the volatage

PVT(process, voltage and temperature) are the factors that determines how the Silicon works.
Process is the variations due to fabrication.
<img width="960" alt="libcode1" src="https://user-images.githubusercontent.com/86144443/123262249-4abf1b80-d515-11eb-9bd6-1a7f0529d179.PNG">

The units of time, voltage, power, current, capacitance, resistance are mentioned clearly.

.lib contains lot of standard cells. For instance, there's a and2_0 gate whose definition looks like this. The definition contains the features of the gate.(leakage power of all the combination of the inputs, area number, power port information, description of each input pin)
<img width="960" alt="and2_0_lib" src="https://user-images.githubusercontent.com/86144443/123263499-a5a54280-d516-11eb-9366-97f7def314cf.PNG">
<img width="960" alt="and2_0lib1" src="https://user-images.githubusercontent.com/86144443/123264092-501d6580-d517-11eb-854a-99f17cb4268a.PNG">
<img width="960" alt="and2_0lib2" src="https://user-images.githubusercontent.com/86144443/123264941-26187300-d518-11eb-9c6a-edb3860c824f.PNG">

There are various flavors of gate like and2_0, and2_1, and2_2, and2_4. The area increases, leakage power increases, and delay decreases as we move from and2_0 to and2_4 as can be seen in the following images.
<img width="960" alt="and2_0_lib" src="https://user-images.githubusercontent.com/86144443/123267001-26b20900-d51a-11eb-8f87-94121c3d94e8.PNG">
<img width="960" alt="and2_1" src="https://user-images.githubusercontent.com/86144443/123267026-2ade2680-d51a-11eb-850c-2860018d6cf8.PNG">
<img width="960" alt="and2_2" src="https://user-images.githubusercontent.com/86144443/123267217-62e56980-d51a-11eb-8278-7bc10658f4df.PNG">
<img width="959" alt="and2_4" src="https://user-images.githubusercontent.com/86144443/123267472-ad66e600-d51a-11eb-8cdf-f1cac6253844.PNG">

## Hierarchical vs Flat Synthesis
<img width="959" alt="Capture1" src="https://user-images.githubusercontent.com/86144443/123268018-4695fc80-d51b-11eb-9850-7511ca2d7b78.PNG">

As we can see in this code, it has two submodules. The module multiple_modules is instantiating sub_module1 and sub_module2. Now, we will be synthesizing this RTL design.

<img width="956" alt="Capture2" src="https://user-images.githubusercontent.com/86144443/123268748-f4091000-d51b-11eb-8fe7-7be2a169b5d0.PNG">

<img width="325" alt="summary1" src="https://user-images.githubusercontent.com/86144443/123269010-303c7080-d51c-11eb-8dd1-d15f563fa4d8.PNG">
<img width="517" alt="summary2" src="https://user-images.githubusercontent.com/86144443/123269031-35012480-d51c-11eb-8b84-becca54a4024.PNG">
<img width="555" alt="Capture3" src="https://user-images.githubusercontent.com/86144443/123269397-932e0780-d51c-11eb-9b8a-6aa65a156adf.PNG">

<img width="960" alt="netlist_multiple_module" src="https://user-images.githubusercontent.com/86144443/123288572-53bbe700-d52d-11eb-8c48-3085fd8c7951.PNG">
u1 and u2 are instance of submodule1 and submodule2 respectively. This is the hierarchical design. The hierarchies if modules are preserved.
On writing the generated netlist to a verilog file we get the following 

<img width="553" alt="Capture4" src="https://user-images.githubusercontent.com/86144443/123272512-6e875f00-d51f-11eb-92b9-3eb9dfc95485.PNG">
<img width="958" alt="netlist_verilog1" src="https://user-images.githubusercontent.com/86144443/123290235-b9f53980-d52e-11eb-91cb-dfc50989f907.PNG">
<img width="960" alt="netlist_verilog2" src="https://user-images.githubusercontent.com/86144443/123290281-c24d7480-d52e-11eb-9d3f-2567561c3859.PNG">
<img width="960" alt="netlist_verilog3" src="https://user-images.githubusercontent.com/86144443/123290310-c8dbec00-d52e-11eb-986c-abfd434d938c.PNG">

Here we are inferring a low power cell 'sky130_fd_sc_hd__lpflow_inputiso1p_1' which is a low power or gate.

Now we will flatten the netlist using command 'flatten'.

<img width="960" alt="flat_netlist" src="https://user-images.githubusercontent.com/86144443/123293358-89fb6580-d531-11eb-88bb-9ba0bd69baed.PNG">

We get the following 

<img width="500" alt="flatten_verilog" src="https://user-images.githubusercontent.com/86144443/123292279-861b1380-d530-11eb-9513-44ad66bb8f2a.PNG">
<img width="498" alt="flatten_verilog1" src="https://user-images.githubusercontent.com/86144443/123296601-684fad80-d534-11eb-97b2-0a20ed740083.PNG">



Now, there is direct instatiation of and gate and low power or gate. There is no u1 and u2 hierarchy.

We can also synthesize just one sub-module by using command synth -top <module_name>. Module level synthesis is preferred when we have multiple instances of same module. If our design is very large, and the tool is not doing good work with this large design. Then, we give one module by one module to the tool and it synthesizes optimized netlist for the same. We can then stich together all the netlist at the top level.

![image](https://user-images.githubusercontent.com/86144443/123295094-05114b80-d533-11eb-90da-2a07e6263b7e.png)

## Various Flop Coding styles and optimization
There are various combinational circuit blocks in a design. The combinational blocks, however suffer from glitching issues. 

![image](https://user-images.githubusercontent.com/86144443/123299840-8a96fa80-d537-11eb-8cb4-9fcddcd63d0c.png)

As seen above, glitch is the momentarily change in the output which is undesired. To overcome this issue we introduce flip flops in our design. 
Flip flops stores the value of the output and the value changes only at the edge of the clock. The output of the flop will the stable. The combinational circuit sees stable input and the glitch is taken care of. 

If the initial state of the flop is unknown, the combinational circuit will evaluate it to garbage value. Reset control pins and set control pins are used to initialize the flop.
 ### Asynchronous reset and asynchronous set
 
 The verilog code for asynchronous reset:
 
![image](https://user-images.githubusercontent.com/86144443/123301156-f75ec480-d538-11eb-92f2-1bd676a864c7.png)

As seen, the sensitivity list has reset and clock both. So, if there is any change is reset signal, the always block will be executes and the output 'q' will go low.

In asynchronous reset, the reset will be evaluated irrespective of the positive edge of the clock. The output q will go to 0 whenever it sees reset going to '1'. It will not wait for the next positive edge of the clock. 

<img width="960" alt="async_iverilog_gtk" src="https://user-images.githubusercontent.com/86144443/123301419-4147aa80-d539-11eb-9f22-8fd173c95078.PNG">
<img width="759" alt="wave_async_reset" src="https://user-images.githubusercontent.com/86144443/123301448-4a387c00-d539-11eb-9181-9ee0b57df9ba.PNG">

On synthesizing the RTL design we get the following netlist generated:
![image](https://user-images.githubusercontent.com/86144443/123306347-ce413280-d53e-11eb-938a-0bafe215e906.png)

There is an inverter before the reset input because in our code, we have used active high reset signal but in standard D flip flop cells, reset is active low. 


In asynchronous set, the set will be evaluated irrespective of the positive edge of the clock. The output q will go to '1' whenever it sees set going to '1'. It will not wait for the next positive edge of the clock. 
![image](https://user-images.githubusercontent.com/86144443/123304009-0e52e600-d53c-11eb-86a3-7ea6731b25bd.png)

![image](https://user-images.githubusercontent.com/86144443/123303326-51f92000-d53b-11eb-8200-aea9c04c963a.png)

On synthesizing the asynchronous set RTL design, we get the following netlist:
![image](https://user-images.githubusercontent.com/86144443/123306889-78b95580-d53f-11eb-8d60-c5bc5b67be33.png)


### Synchronous set and synchronous reset
Verilog code for D flip flop with synchronous reset:

![image](https://user-images.githubusercontent.com/86144443/123304799-fdef3b00-d53c-11eb-8b8e-8f59da58680d.png)

The sensitivity list here has only the clock signal. So the always block will be executed only if there is a positive edge of the clock.
In synchronous reset, the reset is going high, but the output q is not immediately going low, it is waiting for the next positive edge of the clock, and then going low. This can be observed in the following waveform.

![image](https://user-images.githubusercontent.com/86144443/123304388-88836a80-d53c-11eb-8b22-05356380675d.png)

If both clock and 'd' input are high on the positive edge of the clock, preference is given to sync_reset because it is there in the if portion.

![image](https://user-images.githubusercontent.com/86144443/123307393-11e86c00-d540-11eb-8b1a-268364640010.png)

### Some interesting optimizations

#### Multiplication of a three bit number by 2

![image](https://user-images.githubusercontent.com/86144443/123309748-ed41c380-d542-11eb-9ddf-2526a901966b.png)

No memories, no cells are synthesized for mul2. Number of cells inferred is 0.
![image](https://user-images.githubusercontent.com/86144443/123310087-588b9580-d543-11eb-845d-05e8ba8018aa.png)

On using abc -liberty command, it says 'don't call ABC as there is nothing to map'.
![image](https://user-images.githubusercontent.com/86144443/123310300-9092d880-d543-11eb-884a-a6ff4768fc2c.png)

On viewing the netlist, we get 'a' appended with one bit '0' as 'y', which is expected. No hardware is generated.
![image](https://user-images.githubusercontent.com/86144443/123310476-bddf8680-d543-11eb-8604-35497b82a3bd.png)

On writing the netlist:

![image](https://user-images.githubusercontent.com/86144443/123319673-57139a80-d54e-11eb-88a3-915233c6e76f.png)

Hence, y is 'a' appended with a one bit '0'.
#### Multiplication of a 3 bit number with 9
The verilog code:
![image](https://user-images.githubusercontent.com/86144443/123322243-ac04e000-d551-11eb-8444-02ab6a885340.png)


NO CELLS ARE MAPPED
![image](https://user-images.githubusercontent.com/86144443/123320869-f1c0a900-d54f-11eb-9699-30677c2af317.png)
Netlist:
![image](https://user-images.githubusercontent.com/86144443/123321126-351b1780-d550-11eb-931c-d3e9c840dca5.png)
So we see that the result is {a,a}. No hardware is used.
On writing the netlist:
![image](https://user-images.githubusercontent.com/86144443/123321284-698ed380-d550-11eb-9839-ba56232f78cf.png)

These are very special cases of optimizations and they are very important because no hardware is required at all to implement the logic function, just some rewiring of signals will serve the purpose.

# Day 3: Combinational and Sequential Optimizations

## Introduction to optimizations

## Combinational logic optimizations

The files used in this lab:
![image](https://user-images.githubusercontent.com/86144443/123331620-2424d300-d55d-11eb-8b13-bf67c3a97a33.png)

Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123331769-520a1780-d55d-11eb-8e6c-bbf871ada77c.png)

opt_clean -purge command is used for constant propagation and optimizations (used after synth -top command). After that we link it to liberty.

Netlist:
![image](https://user-images.githubusercontent.com/86144443/123332475-2a677f00-d55e-11eb-95dc-800e4e50e907.png)

We are geting an AND gate which is the optimized version of design.

Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123332086-af05cd80-d55d-11eb-9dd8-4f2b909ecaa0.png)

Netlist:
![image](https://user-images.githubusercontent.com/86144443/123333224-15d7b680-d55f-11eb-92da-ba56067c610a.png)

Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123333436-5fc09c80-d55f-11eb-87fe-ccad44c6e37b.png)
 
 Effectively I should get y=abc
 
 Netlist:
 ![image](https://user-images.githubusercontent.com/86144443/123333797-d52c6d00-d55f-11eb-9c8d-5f6bc9e760f6.png)

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123334021-1b81cc00-d560-11eb-881e-68a6939d8264.png)
Netlist generated:

![image](https://user-images.githubusercontent.com/86144443/123334377-a2cf3f80-d560-11eb-8b31-00cb81881156.png)

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123334534-e0cc6380-d560-11eb-9faa-a546c428267b.png)

On optimizing the above design we get y= c or (a and b)
Netlist generated:

![image](https://user-images.githubusercontent.com/86144443/123335294-e1b1c500-d561-11eb-9bbb-79b877fa257c.png)

The netlist generated is the same as y= c or (a and b).

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123336105-08bcc680-d563-11eb-9721-42fa2564907a.png)

This should be evaluated to zero.

Netlist without flattening:

![image](https://user-images.githubusercontent.com/86144443/123336464-85e83b80-d563-11eb-8be2-b0be8a53cf85.png)

Netlist with flattening:

![image](https://user-images.githubusercontent.com/86144443/123337017-45d58880-d564-11eb-8ad8-b86d4999e76b.png)

## Sequential logic optimizations

Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123401262-3dad3580-d5c4-11eb-877a-61b788b9f21d.png)
This is a sequential constant.
Waveform:
![image](https://user-images.githubusercontent.com/86144443/123402516-700b6280-d5c5-11eb-9bd9-e1d80bdc6472.png)

Reset went low, but q does not go high immediately, it is waiting for the next rising edge of the clock. This circuit will infer a flop.

Netlist synthesized:
![image](https://user-images.githubusercontent.com/86144443/123406762-df368600-d5c8-11eb-92a2-5ae8a5ac05ea.png)

A d-flip flop is generated. d is connected to '1'. This particular library cell is expecting a active low reset, but we have coded an active high reser, so tool is inferring a inverter passing through the reset signal. 


Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123401878-d2b02e80-d5c4-11eb-82d4-9bd2153203d0.png)
Waveform:

![image](https://user-images.githubusercontent.com/86144443/123403145-1a838580-d5c6-11eb-9395-964679d9d65d.png)
q is always 1.

![image](https://user-images.githubusercontent.com/86144443/123407484-a814a480-d5c9-11eb-81bb-e2a03b42ac22.png)
No cells are inferred here on synthesizing the rtl code as can be seen above.
Netlist:

![image](https://user-images.githubusercontent.com/86144443/123407711-e0b47e00-d5c9-11eb-93ab-9727411b69b5.png)

No D-Flip Flop is inferred.

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123408297-7a7c2b00-d5ca-11eb-97cc-2b007cf81c40.png)

Waveform:
![image](https://user-images.githubusercontent.com/86144443/123431171-9c83a680-d5e6-11eb-833e-e1a3f725cd0b.png)

When the reset goes to '0', 'q1' goes to '1' at the next positive edge of the clock. But there is a clock-to-q delay in the flip flop whose output is 'q1', so 'q' remains '0' for one clock cycle and then goes to '1', that is follows 'q1' at the next positive edge of the clock. 

![image](https://user-images.githubusercontent.com/86144443/123432004-86c2b100-d5e7-11eb-8682-774739721ac0.png)

![image](https://user-images.githubusercontent.com/86144443/123432334-e751ee00-d5e7-11eb-8541-11f34b8ef56b.png)

So two flip flops are inferred. 

So, not every flop that has a constant at the input will get optimised. We need to look at the set and reset connections at the input of the flop and see whether 'q' is getting constant values or they will change.

Few more examples!

![image](https://user-images.githubusercontent.com/86144443/123432961-9262a780-d5e8-11eb-8ee1-d58dfb87b3da.png)

![image](https://user-images.githubusercontent.com/86144443/123433362-01d89700-d5e9-11eb-8eba-c8d30d64dc3a.png)

![image](https://user-images.githubusercontent.com/86144443/123433545-33e9f900-d5e9-11eb-9899-f17edab913cc.png)

![image](https://user-images.githubusercontent.com/86144443/123433684-59770280-d5e9-11eb-8d7e-62360a93c1dc.png)

No flip flops are inferred in this example.

![image](https://user-images.githubusercontent.com/86144443/123433870-84f9ed00-d5e9-11eb-835a-7962845f29db.png)

![image](https://user-images.githubusercontent.com/86144443/123434480-284b0200-d5ea-11eb-8df9-783860757e20.png)


![image](https://user-images.githubusercontent.com/86144443/123434046-b07cd780-d5e9-11eb-9bee-281ad228b864.png)

![image](https://user-images.githubusercontent.com/86144443/123434187-d609e100-d5e9-11eb-8b92-57c83088042a.png)


## Unused output optimization

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123443640-b5468900-d5f3-11eb-8a55-78ee8ce70e64.png)

This is a 3-bit UP counter. 
count[2] and count[1] are unused here. They are not affecting the ouput in anyway. Because, q is assigned to count[0]. 
count[0] is toggling on every clock cycle.

![image](https://user-images.githubusercontent.com/86144443/123444567-a9a79200-d5f4-11eb-8402-d11f51a36eae.png)

3- bit counter should have 3 flops, but, here, only one flop is inferred.

![image](https://user-images.githubusercontent.com/86144443/123445210-4c601080-d5f5-11eb-838d-2a268537a290.png)

One flop is inferred. The output q is connected to count[0], which is passing through an inverter and feedbacked to input 'd' of flip flop. The unused bits count[1] and count[2] are completely optimised off. The output related to the primary output only have relevance in the circuit. 

Now, 
We make a small change in the code and assign q as (count[2:0]==3'b100)
Now all the three bits of the counter are used for estimating the output. So, three flops should be genrated.
![image](https://user-images.githubusercontent.com/86144443/123504394-a1903680-d676-11eb-9364-c31df52b66d8.png)

![image](https://user-images.githubusercontent.com/86144443/123504938-ea95ba00-d679-11eb-86a9-3ddbef5fd9ee.png)

Three flops are inferred.

![image](https://user-images.githubusercontent.com/86144443/123505009-4bbd8d80-d67a-11eb-92bb-b28f04dcb2ea.png)

q= count[2]count[1]'count[0]. which is generated using a three input NOR gate and is connected to q.

Another example from assessment:

Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123522691-848e4e80-d6dc-11eb-9b0c-f2084cab1999.png)

Waveform:

![image](https://user-images.githubusercontent.com/86144443/123522786-36c61600-d6dd-11eb-8ad9-cc69c83a7fa0.png)


Generated netlist:

![image](https://user-images.githubusercontent.com/86144443/123522726-c4edcc80-d6dc-11eb-9c6a-a51bb868d71e.png)

Here the output is optimized to 0, as the condition of q never meets. The count value is either '100' or '000'. It is never equal to '101'.

# Day4: GLS, blocking vs non blocking and Synthesis-Simulation mismatch

## GLS, Synthesis-Simulation mismatch and Blockin/Non-blocking statements

Gate Lvel Simulation is basically running the testbench with Netlist as Design under test. If the logical correctness of design after synthesis is okay, then the waveform generated after gate level simulation will be same as RTL simulation. 

Gate Level Simulation also helps in ensuring if the timing of the design is met. For this, GLS needs to be run with delay annotations. The gate level verilog models needs to be delay annotated, then GLS can be used for timing violations.

The reason for validating the functionality of the netlist is because, in certain cases there are synthesis simulation mismatch which occurs due to the following reasons:
- Missing sensitivity list
- incorrect usage of blocking vs non blocking assignments
- non standard verilog coding

## Labs on GLS and synthesis simulation mismatch
### Example 1
Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123511623-f2685500-d69f-11eb-9611-e78132187dee.png)

This will be a 2:1 MUX as can be visualized from the wwaveform.

![image](https://user-images.githubusercontent.com/86144443/123511684-63a80800-d6a0-11eb-9244-34d8ba9f061b.png)

When sel=0, output is following i0, when sel=1, output is following i1. 

![image](https://user-images.githubusercontent.com/86144443/123511782-f052c600-d6a0-11eb-8a59-85cd90f9b0c5.png)

Clearly, a MUX is inferred.

![image](https://user-images.githubusercontent.com/86144443/123513327-50019f00-d6aa-11eb-8d47-e7615b22842e.png)

Now we will write the netlist to a verilog file ternary_operator_mux_net.v
Now we will do GLS. To do gls we need to invoke iverilog with verilog models (primitives.v and sky130_fd_sc_hd.v), netlist and the testbench.

![image](https://user-images.githubusercontent.com/86144443/123513668-ea161700-d6ab-11eb-8160-13169f9314a3.png)

![image](https://user-images.githubusercontent.com/86144443/123513687-031ec800-d6ac-11eb-87d5-56639986d965.png)

The GLS output is matching with the RTL simulation output. No synthesis simulation mismatch here.

### Example 2:
Let's take an example of synthesis simulation mismatch due to missing sensitivity list.
Verilog code:

![image](https://user-images.githubusercontent.com/86144443/123513725-4ed17180-d6ac-11eb-8e3b-5b6fec48bcbf.png)

![image](https://user-images.githubusercontent.com/86144443/123513773-a53eb000-d6ac-11eb-831f-f46b9eb11337.png)

Clearly this is not behaving like a MUX. The changes of i0 and i1 are not being sensed because they are not present in the sensitivity list. The simulation is showing a dual edge flop kind of behaviour. 

Now if we synthesize the RTL and write the netlist and then view its corresponding waveform will be like

![image](https://user-images.githubusercontent.com/86144443/123514485-e638c380-d6b0-11eb-9900-4ce7293a1112.png)

Here, the waveform is entirely like that of a 2:1 MUX! So, vividly, because of the missing sensitivity list, there is a synthesis simulation mismatch.

## Labs on synth-sim mismatch for blocking statement

Now we will see a case of synthesis simulation mistach due to incorrect use of blocking statements:

![image](https://user-images.githubusercontent.com/86144443/123514763-19c81d80-d6b2-11eb-986a-aba21affb6df.png)

Aim was to create the logic d= (a or b) and c 
We have used blocking statements, so first d=x & c will be executed and then x= a or b will be executed.

![image](https://user-images.githubusercontent.com/86144443/123515039-97405d80-d6b3-11eb-9ca5-74bb3a5541ce.png)

At the instant shown on the waveform, a=0, b=0, c=1. So the value of d should be 0. But in the waveform, the value of d is 1. As the code is evaluating d= x&c first, the previous value of x ( x= a or b) will be used which is '1'. So we get tthe output d=1. So the circuit is behaving as there is a flop for storing the value of x= a or b.

![image](https://user-images.githubusercontent.com/86144443/123515338-a7a50800-d6b4-11eb-82d1-c7f492aa68fa.png)
The netlist is straight forward. A 2 input OR gate followed by a AND gate. No flop is inferred.
On writing the netlist and then observing the waveform:

![image](https://user-images.githubusercontent.com/86144443/123515537-737e1700-d6b5-11eb-83b1-c15b7d729d34.png)

(A1=a, A2=b. B=c, X=d)
when a=0, b=0, c=1, the vaue of output d=0. 
So a synth-sim mismatch is there which is caused by blocking statements.

So blocking statements should be used with utmost care and clarity in verilog. 

# Day5: If, case, for loop and for generate

## If case constructs

If is used to create priority logic. However, if the if statements are not incompletely written it wil infer latches, which is undesirable.

Same goes with the case statements. If there is an incomplete case statement, latch will be inferred. So, we have to make sure to code case with default statement. 
Also if there is a partial assignment of output, lateches will be inferred. Let's hop on to labs to see how these things look like!

## Labs on incomplete if case

### Example 1:
Verilog code:
![image](https://user-images.githubusercontent.com/86144443/123525394-f0c57e00-d6ed-11eb-9449-b97c279a672b.png)

There is no else part coded. This looks like a D latch. i0 acts as enable.

![image](https://user-images.githubusercontent.com/86144443/123525540-1d2dca00-d6ef-11eb-88de-0c1a44aa0b59.png)


Whenever i0 is low, the output y is latched to either 1 or 0 (just the previous value of i1), it is constant. When i0 is high, y is following i1.

![image](https://user-images.githubusercontent.com/86144443/123525568-50705900-d6ef-11eb-9269-59d42947dec0.png)

Our aim was to code a MUX, but the tool is inferring a latch! 

![image](https://user-images.githubusercontent.com/86144443/123525593-7e559d80-d6ef-11eb-8194-6f26c9feec5c.png)

Its a D latch with i0 as enable. 
So a latch is inferred if there is an incomplete if.

### Example 2:

![image](https://user-images.githubusercontent.com/86144443/123535270-5d696880-d740-11eb-97b7-3ba8d01a3e50.png)

The else statement is missing, so we expect a latch to be inferred.

![image](https://user-images.githubusercontent.com/86144443/123535902-d66abf00-d744-11eb-85a4-7efd87d7f22d.png)



When i0 is high, output is exactly following i1.
When i0 is low, i2 is the select line. When i2 is low, output is constant(0 or 1). When i2 goes high, output follows i3. So a latch is inferred because there is no else statement.


![image](https://user-images.githubusercontent.com/86144443/123535837-3b71e500-d744-11eb-93a4-2b3fd9416f5c.png)


A D-latch with active low enable is inferred. The logic for the enable pin of the latch is i0 NOR i2. That is when both i0 and i2 are low, EN=1 and the output is latched and the previous stored value is the output. Otherwise, in the transparent state(EN=0) it's a function of i0, i1 and i3.

## Labs on incomplete overlapping case 

### Example 1:

![image](https://user-images.githubusercontent.com/86144443/123535962-2fd2ee00-d745-11eb-99af-a951549ba8fe.png)

The case statement will transform into a MUX. However, the cases are not complete, so a latch is expected to infer. 

![image](https://user-images.githubusercontent.com/86144443/123536231-c358ee80-d746-11eb-8586-b9d105a68201.png)

When sel=00, output is following i0.
When sel=01, output is following i1.
But when sel= 10 or sel=11, the output is latched to the previous value of y.

![image](https://user-images.githubusercontent.com/86144443/123536329-47ab7180-d747-11eb-9876-59bc2de27de9.png)

So, as we expected, a latch with active low enable is inferred. The enable pin of the latch is sel[1]. Latching action is performed whenever sel[1]=1. The D pin of the latch is the MUX logic generated by the case statement.

### Example 2:

![image](https://user-images.githubusercontent.com/86144443/123536451-01a2dd80-d748-11eb-95e3-7707df0305bd.png)

Here, a default statement is included. So, the missing combinations of sel inputs will be taken care by default and no latch will be generated. 

![image](https://user-images.githubusercontent.com/86144443/123536546-79710800-d748-11eb-9b18-8f8651977995.png)

When sel=00, output is following i0.
When sel=01, output is following i1.
When sel=10 and sel=11, output is following i2.
Default statement helps escape the letching action!

![image](https://user-images.githubusercontent.com/86144443/123536597-e7b5ca80-d748-11eb-9659-298f74a35aa5.png)

No latch is inferred. The logic generated is that of a 4*1 MUX.

### Example 3:

![image](https://user-images.githubusercontent.com/86144443/123536653-598e1400-d749-11eb-81e8-48d848cd30d3.png)

The output x is not assigned for sel=01. Hence, a latch will be inferred.

![image](https://user-images.githubusercontent.com/86144443/123538510-da054280-d752-11eb-8270-c7182542d6b9.png)

y is not having any latch in its path. 
output of x is having a latch. EN= (sel[1]'.sel[0])'= seal[1]+sel[0]'. 

### Example 4:

![image](https://user-images.githubusercontent.com/86144443/123538697-bf7f9900-d753-11eb-9381-f11450ea429e.png)

When sel= 10, the fourth case can also be executed. The simulator will be confused and there will be a synth-sim mismatch. 

![image](https://user-images.githubusercontent.com/86144443/123538842-54829200-d754-11eb-9bd3-2af538b36adc.png)

When we perform the RTL simulation, we get the above waveform. When sel=11, the simulator output y is neither following i2 nor i3, it is constant. The tool is getting confused here.

Now, we perform synthesis.

![image](https://user-images.githubusercontent.com/86144443/123538940-cd81e980-d754-11eb-8e45-7cb6c1fa41dc.png)

![image](https://user-images.githubusercontent.com/86144443/123539143-dc1cd080-d755-11eb-9449-6163efbc95ca.png)

No latches are inferred, as the cases are complete. There are overlapping cases. 

Now we write the netlist to a verilog file and perform gate level simulation.

![image](https://user-images.githubusercontent.com/86144443/123539077-96600800-d755-11eb-8aed-fe8161b682cf.png)

In GLS, the simulator output is following i3 when sel=11. So, clearly there is synth-sim mismatch. 

## For loop and for generate 

For loop:
-  used for evaluating expressions.
-  In verilog, for loop is always used inside always block. 
-  For loop doesnot generate/instantiate hardware.
-  very useful for implementing wide MUX/DeMUX

For generate:
- used outside always block
- it is used for replicating/instantiating harware, it does not evaluate expressions

## Labs on for and for generate

### Example 1:

![image](https://user-images.githubusercontent.com/86144443/123540508-f8703b80-d75c-11eb-8008-29a54d2d3000.png)

Here, a 4:1 MUX is generated using for loop. 

![image](https://user-images.githubusercontent.com/86144443/123540607-8a784400-d75d-11eb-843f-b71ab3ac73aa.png)

The waveform is perfectly demonstrating a 4:1 MUX. 

![image](https://user-images.githubusercontent.com/86144443/123540999-9b29b980-d75f-11eb-895b-a1779d9ceeed.png)

A latch is inferred, it is always in transparent mode with active low enable.

### Example 2:

![image](https://user-images.githubusercontent.com/86144443/123542672-8bfb3980-d768-11eb-9bfd-40d50e879ccb.png)

![image](https://user-images.githubusercontent.com/86144443/123542689-a46b5400-d768-11eb-8a12-88e353176f09.png)

Two ways of writing demux, one using case statements and other if using for loop. 

Waveform generated for for loop:

![image](https://user-images.githubusercontent.com/86144443/123542826-455a0f00-d769-11eb-877b-973a6ff133cb.png)

Waveform generated for demux with case statement:

![image](https://user-images.githubusercontent.com/86144443/123542921-c1545700-d769-11eb-81a6-f013ce8b21c4.png)

Both are same. As can be seen, for is a simple, concise elegant way to write demux or mux. It avoids the repetition of lines.

### Example 3:

4 bit Ripple Carry Adder

![image](https://user-images.githubusercontent.com/86144443/123545399-6cb6d900-d775-11eb-835d-5ec53dd90520.png)

![image](https://user-images.githubusercontent.com/86144443/123545415-82c49980-d775-11eb-9818-625fa8552f99.png)

We are replicating the hardware of full adder 8 times with the help of generate for.

![image](https://user-images.githubusercontent.com/86144443/123545534-1b5b1980-d776-11eb-8605-34d357bae14b.png)

From the waveform its clear that addition of num1 and num2 is being performed.

On performing GLS of the netlist, we get the following waveform:

![image](https://user-images.githubusercontent.com/86144443/123545773-7ccfb800-d777-11eb-9359-f39ed5127f21.png)

Our purpose is served, there is no synth-sim mismatch.





