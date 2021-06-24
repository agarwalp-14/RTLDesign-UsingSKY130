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

There are various flavors of gate like and2_0, and2_1, and2_2. The area increases, leakage power increases, and delay decreases as we move from and2_0 to and2_2.
<img width="960" alt="and2_0_lib" src="https://user-images.githubusercontent.com/86144443/123267001-26b20900-d51a-11eb-8f87-94121c3d94e8.PNG">
<img width="960" alt="and2_1" src="https://user-images.githubusercontent.com/86144443/123267026-2ade2680-d51a-11eb-850c-2860018d6cf8.PNG">
<img width="960" alt="and2_2" src="https://user-images.githubusercontent.com/86144443/123267217-62e56980-d51a-11eb-8278-7bc10658f4df.PNG">



