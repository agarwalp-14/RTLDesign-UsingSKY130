
# RTLDesign-UsingSKY130
This is a summary of the RTL Design and Synthesis Workshop that I was a part 

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







