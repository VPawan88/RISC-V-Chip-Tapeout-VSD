# Week 1  Day 4

# GLS, Blocking vs Non-Blocking and Synthesis-Simulation Mismatch

### 1️⃣ Indroduction to Gate Level Simulation

## what is GLS
 -Running the testbench with Netlist as Design Under Test
 -Netlist is logically same as RTL code
  -Same testbench will align with the design
## why GLS
 -verify the logical correctness of design after synthesis
 -Ensuring the timing of the design is met
  -For this GLS needs to be run with delay annotation.
## GLS using iverilog
 -Rest is similar only Gate level verilog models are feed to iverilog
 -If the Gate level models are delay annotated, then we can use GLS for timing validation

### 2️⃣ Synthesis Simulation Mismatch
-Missing sensitivity list
-Blocking Vs Non-Blocking statements
-Non standard verilog coding


### 3️⃣ Implementation of GLS

## 1️. "ternary_operator_mux.v"
```verilog
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
```

## "tb_ternary_operator_mux.v"
```verilog

`timescale 1ns / 1ps
module tb_ternary_operator_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	ternary_operator_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_ternary_operator_mux.vcd");
	$dumpvars(0,tb_ternary_operator_mux);
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
```
## commands
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

<img width="1848" height="503" alt="Screenshot 2025-09-27 050917" src="https://github.com/user-attachments/assets/e32d20b7-8bff-4f6a-a9c9-1f70f9c1976a" />


🛠️ Yosys Commands
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```

<img width="598" height="209" alt="Screenshot 2025-09-27 051531" src="https://github.com/user-attachments/assets/20277ffc-a36a-46de-a3f4-04a928945ec2" />

## GLS
## command
```
iverilog ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v  tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
<img width="1849" height="647" alt="Screenshot 2025-09-27 052434" src="https://github.com/user-attachments/assets/c2086df7-e2ef-4d63-8f0a-6665a8fc7616" />





