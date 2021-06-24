
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












