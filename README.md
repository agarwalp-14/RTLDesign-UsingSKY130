<img width="959" alt="and2_4" src="https://user-images.githubusercontent.com/86144443/123267361-8c9e9080-d51a-11eb-847e-1df6a552ffb8.PNG">
# RTLDesign-UsingSKY130
This is a summary of the RTL Design and Synthesis Workshop that I was a part 

# Day 2: Timing libs, hierarchical vs flat synthesis and efficient 
## Introduction to timing.libs
The name of the library file name:
- sky130 is the name of the library
- **tt** stands for typical 
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

<img width="553" alt="Capture4" src="https://user-images.githubusercontent.com/86144443/123272512-6e875f00-d51f-11eb-92b9-3eb9dfc95485.PNG">





